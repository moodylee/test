swagger: '2.0'
info:
  version: 1.0.0
  title: 'Pick Up API'
schemes:
  - https
basePath: /shippment-service/pickup
produces:
  - application/json
paths:
  /availability:
    get:
      description: |
        Get Package Pickup Availability
      parameters:
        - name: provider
          in: query
          description: service provider, e.g. fedex, usps, etc.
          required: true
          type: string
          enum:
            - fedex
            - ups
            - usps
        - name: storeId
          in: query
          description: physical store Id from which the package will be picked up
          required: true
          type: string
        - name: firstName
          in: query
          description: pick up scheduler first name
          required: true
          type: string
        - name: lastName
          in: query
          description: pick up scheduler last name
          required: true
          type: string
        - name: address1
          in: query
          description: pickup address line1
          required: true
          type: string
        - name: address2
          in: query
          description: pickup address line2
          required: false
          type: string
        - name: city
          in: query
          description: pickup address city
          required: true
          type: string
        - name: state
          in: query
          description: pickup address state
          required: true
          type: string
        - name: zip
          in: query
          description: pickup address zip code
          required: true
          type: string
        - name: country
          in: query
          description: pickup address country code
          required: true
          type: string
        - name: pickupDateTime
          in: query
          description: pickup date time
          required: false
          type: string
          format: date-time
      responses:
        '200':
          description: Successful response
          schema:
            $ref: '#/definitions/PickupAvailability'
        default:
          description: Unexpected error
          schema:
            $ref: '#/definitions/Error'
  /:
    post:
      description: |
        Schedule a pickup
      parameters:
        - in: body
          name: body
          description: Pickup needs to be scheduled in the carrier service provider
          required: true
          schema:
            $ref: "#/definitions/PickupSchedule"
      responses:
        '200':
          description: Successful response
          schema:
            $ref: '#/definitions/PickupSchedule'
        default:
          description: Unexpected error
          schema:
            $ref: '#/definitions/Error'
definitions:
  Pickup:
    type: object
    properties:
      provider:
        type: string
        description: carrier service provider
        enum:
            - fedex
            - ups
            - usps
      storeId:
        type: string
        description: physical store Id from which the package is picked up
      address:
        $ref: '#/definitions/Address'  
      pickupDateTime:
        type: string
        description: pickup date time
        format: date-time

  PickupAvailability:
    allOf:
    - $ref: '#/definitions/Pickup'
    - type: object
      properties:
        requestType:
          type: string
          description: pickup request type, only used by Fedex currently
        numberOfBusinessDays:
          type: string
          description: number of business days, only used by Fedex currently
        closingTime:
          type: string
          description: store closing time, only used by Fedex currently
  PickupSchedule:
    allOf:
    - $ref: '#/definitions/Pickup'
    - type: object
      properties:
        id:
          type: string
          description: pickup schedule id
        readyDateTime:
          type: string
          description: package ready date time
          format: date-time
        closingTime:
          type: string
          description: store closing time
          format: time
        packageCount:
          type: integer
          description: package count
        totalWeightUOM:
          type: string
          description: unit of total weight
        totalWeight:
          type: number
          description: total weight of packages
          format: double

  Address:
    type: object
    properties:
      address1:
        type: string
        description: address line1
      address2:
        type: string
        description: address line2
      city:
        type: string
        description: city
      state:
        type: string
        description: state
      zip:
        type: string
        description: zip
      country:
        type: string
        description: country
  Error:
    type: object
    properties:
      code:
        type: string
      message:
        type: string
