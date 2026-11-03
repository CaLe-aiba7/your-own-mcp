openapi: 3.0.3

info:
  title: Sample Insurance API
  description: Sample API for testing Context7 and MCP integration.
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

paths:

  /customers/{customerId}:
    get:
      summary: Get customer details
      description: Retrieves an existing customer by customer ID.
      parameters:
        - name: customerId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Customer found
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Customer"
        "404":
          description: Customer not found

  /policies:
    post:
      summary: Create insurance policy
      description: Creates a new insurance policy for an existing customer.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreatePolicyRequest"
      responses:
        "201":
          description: Policy created successfully
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Policy"
        "400":
          description: Invalid request

  /policies/{policyId}:
    get:
      summary: Get policy details
      description: Retrieves details of an insurance policy.
      parameters:
        - name: policyId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Policy found
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Policy"
        "404":
          description: Policy not found

  /policies/{policyId}/cancel:
    post:
      summary: Cancel insurance policy
      description: Cancels an active insurance policy.
      parameters:
        - name: policyId
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - cancellationReason
              properties:
                cancellationReason:
                  type: string
      responses:
        "200":
          description: Policy cancelled successfully
        "400":
          description: Policy cannot be cancelled
        "404":
          description: Policy not found

  /claims:
    post:
      summary: Submit insurance claim
      description: Submits a new claim against an existing policy.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateClaimRequest"
      responses:
        "201":
          description: Claim submitted successfully
        "400":
          description: Invalid claim
        "404":
          description: Policy not found

components:

  schemas:

    Customer:
      type: object
      properties:
        customerId:
          type: string
        firstName:
          type: string
        lastName:
          type: string
        email:
          type: string

    CreatePolicyRequest:
      type: object
      required:
        - customerId
        - productCode
        - effectiveDate
      properties:
        customerId:
          type: string
        productCode:
          type: string
        effectiveDate:
          type: string
          format: date

    Policy:
      type: object
      properties:
        policyId:
          type: string
        customerId:
          type: string
        productCode:
          type: string
        status:
          type: string
          enum:
            - ACTIVE
            - CANCELLED
            - EXPIRED
        effectiveDate:
          type: string
          format: date

    CreateClaimRequest:
      type: object
      required:
        - policyId
        - claimType
        - incidentDate
      properties:
        policyId:
          type: string
        claimType:
          type: string
        incidentDate:
          type: string
          format: date
        description:
          type: string