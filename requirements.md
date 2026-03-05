# Requirements Document: FARM2FORK AI-Powered Traceability Platform

## Introduction

FARM2FORK is an AWS AI-powered farm-to-consumer traceability platform that provides food transparency through QR code tracking. The system operates in two distinct modes: Farmer Mode (for data creation) and Consumer Mode (for trust verification). The platform leverages AWS AI services to generate safety scores and provide comprehensive food safety analysis.

## Glossary

- **System**: The FARM2FORK platform including frontend, backend, and AWS services
- **Farmer_Mode**: The green-themed interface for farmers to create and manage crop batches
- **Consumer_Mode**: The blue-themed interface for consumers to scan and verify food safety
- **Crop_Batch**: A collection of crop data including images, treatment history, and metadata
- **QR_Code**: A unique identifier linking to crop batch information
- **AI_Safety_Engine**: The AWS AI-powered service that analyzes crop data and generates safety scores
- **Safety_Score**: A numerical value (0-100) indicating food safety level
- **Risk_Level**: A categorical classification (Safe/Moderate/Risk) based on safety score
- **Farmer_Dashboard**: The main interface for farmers to manage crop batches
- **Verification_Page**: The consumer-facing page displaying safety analysis results
- **OTP**: One-time password for farmer authentication (demo value: 0000)
- **Harvest_Date**: The date when the crop was harvested, recorded by the farmer
- **AI_Consumption_Advisor**: AWS AI service that provides consumption recommendations and safety guidance to consumers

## Requirements

### Requirement 1: Mode Selection

**User Story:** As a user, I want to select between Farmer Mode and Consumer Mode, so that I can access the appropriate interface for my role.

#### Acceptance Criteria

1. WHEN a user visits the home page, THE System SHALL display two large mode selection cards
2. WHEN displaying mode selection cards, THE System SHALL use green theme for Farmer Mode and blue theme for Consumer Mode
3. WHEN a user clicks Farmer Mode card, THE System SHALL navigate to the farmer login page
4. WHEN a user clicks Consumer Mode card, THE System SHALL navigate to the consumer scan page
5. THE System SHALL display the mode selection page at the root URL path

### Requirement 2: Farmer Authentication

**User Story:** As a farmer, I want to log in using OTP authentication, so that I can securely access my dashboard.

#### Acceptance Criteria

1. WHEN a farmer enters an OTP, THE System SHALL validate it against the authentication service
2. WHEN the OTP is "0000", THE System SHALL authenticate the farmer successfully (demo mode)
3. WHEN authentication succeeds, THE System SHALL navigate to the Farmer Dashboard
4. WHEN authentication fails, THE System SHALL display an error message and maintain the login state
5. THE System SHALL provide a mobile-first login interface with large input fields and buttons

### Requirement 3: Crop Batch Creation

**User Story:** As a farmer, I want to create crop batches with images and treatment details, so that I can generate traceable QR codes for my produce.

#### Acceptance Criteria

1. WHEN a farmer creates a crop batch, THE System SHALL allow image uploads for crop details, pesticide packages, and fertilizer packages
2. WHEN a farmer uploads seed packet images, THE System SHALL use Amazon Textract to extract crop name, variety, and planting details
3. WHEN a farmer uploads pesticide package images, THE System SHALL use Amazon Textract to extract pesticide name and dosage information
4. WHEN a farmer uploads fertilizer package images, THE System SHALL use Amazon Textract to extract fertilizer name and quantity information
5. WHEN text extraction is complete, THE System SHALL pre-fill form fields with extracted product details for farmer review
6. WHEN entering treatment information, THE System SHALL require farmers to manually input application dates for pesticides and fertilizers
7. WHEN creating a crop batch, THE System SHALL require farmers to input the harvest date
8. WHEN extraction fails or is incomplete, THE System SHALL allow farmers to manually enter or edit all data
9. WHEN a farmer uploads images, THE System SHALL store them in AWS S3
10. THE System SHALL provide a rural-friendly interface with minimal typing requirements through image-based data entry
11. WHEN all required fields are completed, THE System SHALL enable the AI analysis action

### Requirement 4: AI Safety Analysis

**User Story:** As a farmer, I want the system to analyze my crop data using AI, so that I can provide verified safety information to consumers.

#### Acceptance Criteria

1. WHEN a farmer requests AI analysis, THE AI_Safety_Engine SHALL process crop name, farming method, pesticide usage, and application dates
2. WHEN processing crop data, THE AI_Safety_Engine SHALL use Amazon Bedrock to generate a safety score (0-100)
3. WHEN generating safety analysis, THE AI_Safety_Engine SHALL determine risk level (Safe/Moderate/Risk) based on safety score
4. WHEN analysis is complete, THE AI_Safety_Engine SHALL provide a consumer-friendly explanation of food safety risk
5. WHEN crop images are uploaded, THE System SHALL use Amazon Rekognition to analyze crop visual characteristics and detect quality indicators
6. THE System SHALL use Amazon Translate to provide multilingual output in English and Hindi

### Requirement 5: QR Code Generation

**User Story:** As a farmer, I want to generate and download QR codes for my crop batches, so that consumers can scan and verify my produce.

#### Acceptance Criteria

1. WHEN AI analysis is complete, THE System SHALL generate a unique QR code for the crop batch
2. WHEN generating a QR code, THE System SHALL create a unique identifier linking to the crop batch data
3. WHEN a QR code is generated, THE System SHALL store it in AWS S3
4. WHEN a farmer requests download, THE System SHALL provide the QR code as a downloadable image file
5. THE System SHALL associate the QR code with all crop batch metadata in the database

### Requirement 6: Consumer QR Scanning

**User Story:** As a consumer, I want to scan QR codes on food products, so that I can verify food safety and traceability.

#### Acceptance Criteria

1. WHEN a consumer clicks "Scan Food", THE System SHALL open the device camera automatically
2. WHEN the camera opens, THE System SHALL display a QR scanner overlay interface
3. WHEN a QR code is detected, THE System SHALL decode the unique identifier
4. WHEN a QR code is successfully scanned, THE System SHALL display a verification animation
5. WHEN verification is complete, THE System SHALL navigate to the Verification Page
6. THE System SHALL NOT require consumer login or authentication

### Requirement 7: Safety Verification Display

**User Story:** As a consumer, I want to view comprehensive safety information about scanned food products, so that I can make informed purchasing decisions.

#### Acceptance Criteria

1. WHEN displaying verification results, THE System SHALL show the AI Safety Score as a gauge (0-100)
2. WHEN displaying verification results, THE System SHALL show the risk category (Safe/Moderate/Risk)
3. WHEN displaying verification results, THE System SHALL show farmer information
4. WHEN displaying verification results, THE System SHALL show the harvest date
5. WHEN displaying verification results, THE System SHALL show a crop journey timeline
6. WHEN displaying verification results, THE System SHALL show treatment history including pesticides, fertilizers, and application dates
7. WHEN displaying verification results, THE System SHALL show a trust badge
8. WHEN a consumer requests AI consumption advice, THE AI_Consumption_Advisor SHALL provide recommendations on how to consume the product safely
9. WHEN a consumer requests safety guidance, THE AI_Consumption_Advisor SHALL provide instructions on how to remove pesticides
10. WHEN a consumer requests safety information, THE AI_Consumption_Advisor SHALL explain how safe the product is for consumption
11. THE System SHALL use Amazon Bedrock to generate personalized consumption and safety recommendations
12. THE System SHALL use blue theme for all consumer-facing interfaces

### Requirement 8: Multi-Language Support

**User Story:** As a user, I want to use the platform in my preferred Indian language, so that I can understand and interact with the system comfortably.

#### Acceptance Criteria

1. THE System SHALL support English, Hindi, Tamil, Telugu, Kannada, Malayalam, Bengali, Marathi, Gujarati, and Punjabi languages
2. WHEN a user accesses the platform, THE System SHALL detect the browser language and set it as default
3. WHEN a user selects a language, THE System SHALL translate all UI text to the selected language
4. WHEN a user switches language, THE System SHALL persist the language preference in local storage
5. THE System SHALL use Amazon Translate to translate dynamic content including AI-generated safety explanations and consumption advice
6. WHEN displaying crop batch data, THE System SHALL translate field labels and UI elements while preserving original data values
7. THE System SHALL provide a language switcher component accessible from all pages
8. WHEN translating content, THE System SHALL maintain formatting and structure of the original text

### Requirement 9: Backend API Services

**User Story:** As a system, I want to provide RESTful APIs for all platform operations, so that the frontend can interact with backend services and AWS AI services.

#### Acceptance Criteria

1. THE System SHALL provide POST /auth/login endpoint for farmer authentication
2. THE System SHALL provide POST /batch/create endpoint for creating crop batches
3. THE System SHALL provide POST /image/upload endpoint for uploading images to S3
4. THE System SHALL provide POST /ai/analyze endpoint for triggering AI safety analysis
5. THE System SHALL provide POST /qr/generate endpoint for generating QR codes
6. THE System SHALL provide GET /public/{qr_id} endpoint for retrieving public crop batch data
7. THE System SHALL provide POST /ai/consumption-advice endpoint for generating consumer recommendations
8. THE System SHALL provide POST /translate endpoint for translating content to selected language
9. WHEN handling requests, THE System SHALL validate all input parameters
10. WHEN errors occur, THE System SHALL return appropriate HTTP status codes and error messages

### Requirement 10: Data Persistence

**User Story:** As a system, I want to persist all crop batch data and metadata, so that information remains available for verification and traceability.

#### Acceptance Criteria

1. THE System SHALL store crop batch metadata in AWS RDS PostgreSQL database
2. WHEN storing crop batches, THE System SHALL include crop name, farming method, creation date, harvest date, and farmer ID
3. WHEN storing treatment data, THE System SHALL include pesticide/fertilizer names, application dates, and quantities
4. WHEN storing AI analysis results, THE System SHALL include safety score, risk level, and explanation text
5. THE System SHALL store image files in AWS S3 with references in the database
6. THE System SHALL store QR code images in AWS S3 with references in the database
7. WHEN querying by QR ID, THE System SHALL retrieve all associated crop batch data efficiently

### Requirement 10: AWS Infrastructure Deployment

**User Story:** As a developer, I want to deploy the system on AWS infrastructure, so that the platform is scalable, serverless, and production-ready.

#### Acceptance Criteria

1. THE System SHALL deploy the backend API using AWS Lambda with Mangum adapter
2. THE System SHALL expose APIs through AWS API Gateway
3. THE System SHALL serve frontend static files through AWS S3 and CloudFront
4. THE System SHALL connect to AWS RDS PostgreSQL for data persistence
5. THE System SHALL use AWS S3 for image and QR code storage
6. THE System SHALL configure environment variables for all AWS service connections
7. THE System SHALL provide deployment scripts for automated infrastructure setup
8. WHEN deploying, THE System SHALL configure CORS for cross-origin requests

### Requirement 11: Progressive Web App

**User Story:** As a mobile user, I want to use the platform as a Progressive Web App, so that I can access it offline and install it on my device.

#### Acceptance Criteria

1. THE System SHALL implement PWA capabilities with service workers
2. THE System SHALL provide a mobile-first responsive design
3. THE System SHALL support offline functionality for previously viewed data
4. THE System SHALL provide an app manifest for installation
5. THE System SHALL optimize performance for mobile networks
6. WHEN accessing on mobile devices, THE System SHALL provide large touch-friendly buttons
7. THE System SHALL use accessible typography suitable for rural users

### Requirement 12: UI Design and Theming

**User Story:** As a user, I want a modern, accessible interface with appropriate theming, so that I can easily navigate and use the platform.

#### Acceptance Criteria

1. THE System SHALL use green and earth colors for Farmer Mode interfaces
2. THE System SHALL use blue colors for Consumer Mode interfaces
3. THE System SHALL provide smooth animations for state transitions
4. THE System SHALL use large icons for improved accessibility
5. THE System SHALL implement a safety score gauge component
6. THE System SHALL implement a timeline component for crop journey visualization
7. THE System SHALL implement a QR scanner component with camera overlay
8. THE System SHALL follow modern AgriTech startup design patterns
