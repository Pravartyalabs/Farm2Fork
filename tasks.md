# Implementation Plan: FARM2FORK AI-Powered Traceability Platform

## Overview

This implementation plan breaks down the FARM2FORK platform into discrete, incremental coding tasks. The platform will be built using React + TypeScript for the frontend, FastAPI + Python for the backend, PostgreSQL for the database, and AWS services (Lambda, S3, RDS, Bedrock, Textract, Rekognition, Translate) for infrastructure and AI capabilities.

Each task builds on previous work, with testing integrated throughout to validate functionality early. The plan follows a bottom-up approach: infrastructure → backend core → AWS integrations → frontend → end-to-end integration.

## Tasks

- [x] 1. Set up project structure and infrastructure foundation
  - Create root directory structure: `frontend/`, `backend/`, `infrastructure/`, `deployment/`, `docs/`
  - Initialize backend: FastAPI project with virtual environment, requirements.txt
  - Initialize frontend: React + TypeScript with Vite, Tailwind CSS, PWA configuration
  - Create `.env.example` files for both frontend and backend with all required environment variables
  - Set up Git repository with `.gitignore` for Python and Node.js
  - _Requirements: 10.1, 10.2, 10.3, 10.4, 10.5, 10.6_

- [ ] 2. Implement database models and migrations
  - [x] 2.1 Create SQLAlchemy base configuration and database connection
    - Set up database.py with engine, session, and Base
    - Configure connection pooling for RDS PostgreSQL
    - _Requirements: 10.1_
  
  - [x] 2.2 Implement all database models
    - Create Farmer, CropBatch, CropImage, Treatment, SafetyAnalysis, QRCode models
    - Define relationships and foreign keys
    - Add indexes for performance (farmer_id, batch_id, qr_id)
    - _Requirements: 10.2, 10.3, 10.4_
  
  - [x] 2.3 Create Alembic migrations
    - Initialize Alembic
    - Generate initial migration for all tables
    - Create migration script for indexes
    - _Requirements: 10.1_
  
  - [ ]* 2.4 Write property test for database record completeness
    - **Property 15: Database Record Completeness**
    - **Validates: Requirements 10.1, 10.2, 10.3**
  
  - [ ]* 2.5 Write property test for safety analysis storage
    - **Property 16: Safety Analysis Storage Completeness**
    - **Validates: Requirements 10.4**

- [ ] 3. Implement AWS S3 integration for image storage
  - [x] 3.1 Create S3 service class
    - Implement upload_file method with boto3
    - Generate unique file keys with UUID
    - Return S3 URLs for uploaded files
    - Configure bucket CORS for frontend access
    - _Requirements: 3.9, 10.5, 10.6_
  
  - [x] 3.2 Create signed URL generation for secure uploads
    - Implement generate_presigned_url for direct frontend uploads
    - Set expiration time for presigned URLs
    - _Requirements: 3.9_
  
  - [ ]* 3.3 Write property test for image storage persistence
    - **Property 2: Image Storage Persistence**
    - **Validates: Requirements 3.9, 5.3, 10.5, 10.6**

- [ ] 4. Implement AWS Textract integration for OCR
  - [x] 4.1 Create Textract service class
    - Implement extract_from_image method using analyze_document API
    - Parse response to extract raw text and key-value pairs
    - Calculate confidence scores
    - _Requirements: 3.2, 3.3, 3.4_
  
  - [x] 4.2 Implement specialized extraction methods
    - Create extract_seed_packet_info for crop details
    - Create extract_pesticide_info for pesticide packages
    - Create extract_fertilizer_info for fertilizer packages
    - Use pattern matching to structure extracted data
    - _Requirements: 3.2, 3.3, 3.4_
  
  - [ ]* 4.3 Write property test for OCR extraction completeness
    - **Property 1: OCR Extraction Completeness**
    - **Validates: Requirements 3.2, 3.3, 3.4, 3.5**

- [ ] 5. Implement AWS Rekognition integration for image analysis
  - [x] 5.1 Create Rekognition service class
    - Implement analyze_crop_image using detect_labels API
    - Set MinConfidence to 70%
    - Parse labels and categories from response
    - _Requirements: 4.5_
  
  - [x] 5.2 Implement crop quality indicator extraction
    - Map labels to quality indicators (freshness, ripeness, damage, disease)
    - Calculate indicator scores from label confidence
    - _Requirements: 4.5_
  
  - [ ]* 5.3 Write property test for Rekognition label extraction
    - **Property 6: Rekognition Label Extraction**
    - **Validates: Requirements 4.5**

- [ ] 6. Implement AWS Bedrock integration for AI safety analysis
  - [x] 6.1 Create Bedrock service class
    - Configure boto3 client for bedrock-runtime
    - Set model ID to Claude 3 Sonnet
    - Implement invoke_model wrapper with error handling
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  
  - [x] 6.2 Implement safety analysis generation
    - Build comprehensive prompt with crop data, treatments, and Rekognition results
    - Parse JSON response for safety_score, risk_level, explanation
    - Validate score is 0-100 and risk_level is valid enum
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  
  - [x] 6.3 Implement consumption advice generation
    - Build prompt for cleaning instructions, safety tips, consumption recommendations
    - Support language parameter for multilingual advice
    - Parse JSON response
    - _Requirements: 7.8, 7.9, 7.10, 7.11_
  
  - [ ]* 6.4 Write property test for safety score range validity
    - **Property 4: Safety Score Range Validity**
    - **Validates: Requirements 4.2, 4.3**
  
  - [ ]* 6.5 Write property test for AI analysis completeness
    - **Property 5: AI Analysis Completeness**
    - **Validates: Requirements 4.1, 4.4**
  
  - [ ]* 6.6 Write property test for consumption advice generation
    - **Property 9: Consumption Advice Generation**
    - **Validates: Requirements 7.8, 7.9, 7.10, 7.11**

- [ ] 7. Implement AWS Translate integration for multi-language support
  - [x] 7.1 Create Translate service class
    - Configure language code mapping for 10 Indian languages
    - Implement translate_text method
    - Implement translate_batch for multiple texts
    - _Requirements: 4.6, 8.3, 8.5_
  
  - [x] 7.2 Implement verification data translation
    - Create translate_verification_data method
    - Translate safety explanations and consumption advice
    - Preserve data values while translating labels
    - _Requirements: 8.5, 8.6, 8.8_
  
  - [ ]* 7.3 Write property test for translation consistency
    - **Property 10: Multi-Language Translation Consistency**
    - **Validates: Requirements 4.6, 8.3, 8.5**
  
  - [ ]* 7.4 Write property test for translation data preservation
    - **Property 11: Translation Data Preservation**
    - **Validates: Requirements 8.6, 8.8**

- [ ] 8. Implement authentication and farmer management
  - [x] 8.1 Create authentication utilities
    - Implement JWT token generation and validation
    - Create password hashing utilities (for future use)
    - Implement OTP validation (demo mode: "0000")
    - _Requirements: 2.1, 2.2_
  
  - [x] 8.2 Implement farmer CRUD operations
    - Create get_or_create_farmer function
    - Implement farmer lookup by phone
    - _Requirements: 2.1, 2.2_
  
  - [x] 8.3 Write unit tests for authentication
    - Test demo OTP validation
    - Test JWT token generation and validation
    - Test authentication failure cases
    - _Requirements: 2.1, 2.2, 2.4_

- [ ] 9. Implement core API endpoints - Authentication and Image Upload
  - [x] 9.1 Create FastAPI app with CORS configuration
    - Initialize FastAPI app
    - Configure CORS for frontend origin
    - Set up exception handlers
    - _Requirements: 10.8_
  
  - [x] 9.2 Implement POST /api/auth/login endpoint
    - Validate phone and OTP
    - Create or retrieve farmer
    - Generate JWT token
    - Return farmer info and token
    - _Requirements: 2.1, 2.2, 2.3, 9.1_
  
  - [ ] 9.3 Implement POST /api/image/upload endpoint
    - Accept multipart file upload
    - Validate image type parameter
    - Upload to S3
    - Trigger Textract extraction based on image type
    - Return image URL and extracted data
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 9.3_
  
  - [ ] 9.4 Write unit tests for auth and upload endpoints
    - Test login with demo OTP
    - Test login with invalid OTP
    - Test image upload with valid file
    - Test image upload with invalid file type
    - _Requirements: 2.1, 2.2, 2.4, 3.8_
  
  - [ ]* 9.5 Write property test for API input validation
    - **Property 13: API Input Validation**
    - **Validates: Requirements 9.9**

- [ ] 10. Implement core API endpoints - Batch Creation and AI Analysis
  - [ ] 10.1 Implement POST /api/batch/create endpoint
    - Validate all required fields (crop_name, farming_method, harvest_date)
    - Create CropBatch record
    - Create Treatment records for pesticides and fertilizers
    - Create CropImage records
    - Return batch_id
    - _Requirements: 3.1, 3.6, 3.7, 9.2_
  
  - [ ] 10.2 Implement POST /api/ai/analyze endpoint
    - Retrieve batch with all related data
    - Call Rekognition for crop images
    - Call Bedrock for safety analysis
    - Store SafetyAnalysis record
    - Return analysis results
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 9.4_
  
  - [ ] 10.3 Write unit tests for batch and analysis endpoints
    - Test batch creation with valid data
    - Test batch creation with missing required fields
    - Test AI analysis with complete batch
    - Test AI analysis with missing batch
    - _Requirements: 3.6, 3.7, 4.1, 4.2, 4.3, 4.4_
  
  - [ ]* 10.4 Write property test for form validation consistency
    - **Property 3: Form Validation Consistency**
    - **Validates: Requirements 3.6, 3.7, 3.11**

- [ ] 11. Implement QR code generation and public verification endpoints
  - [ ] 11.1 Create QR code generation utility
    - Use qrcode library to generate QR images
    - Generate unique short QR IDs (8-character alphanumeric)
    - Upload QR image to S3
    - _Requirements: 5.1, 5.2, 5.3_
  
  - [ ] 11.2 Implement POST /api/qr/generate endpoint
    - Validate batch has completed AI analysis
    - Generate unique QR ID
    - Create QR code image
    - Store QRCode record
    - Return QR URL and public URL
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 9.5_
  
  - [ ] 11.3 Implement GET /api/public/{qr_id} endpoint
    - No authentication required
    - Retrieve batch by QR ID with all related data
    - Increment scan_count
    - Build timeline from batch events
    - Return complete verification data
    - _Requirements: 6.6, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 9.6, 10.7_
  
  - [ ] 11.4 Implement POST /api/ai/consumption-advice endpoint
    - Retrieve batch by QR ID
    - Call Bedrock for consumption advice
    - Translate advice if language parameter provided
    - Return advice in requested language
    - _Requirements: 7.8, 7.9, 7.10, 7.11, 9.7_
  
  - [ ]* 11.5 Write property test for QR code uniqueness and linkage
    - **Property 7: QR Code Uniqueness and Linkage**
    - **Validates: Requirements 5.1, 5.2, 5.5, 10.7**
  
  - [ ]* 11.6 Write property test for QR code decoding accuracy
    - **Property 8: QR Code Decoding Accuracy**
    - **Validates: Requirements 6.3**

- [ ] 12. Implement translation endpoint and error handling
  - [ ] 12.1 Implement POST /api/translate endpoint
    - Accept text, source_language, target_language
    - Call Amazon Translate
    - Return translated text
    - _Requirements: 8.3, 8.5, 9.8_
  
  - [ ] 12.2 Implement global error handlers
    - Create custom exception classes (AuthenticationError, ValidationError, AIServiceError)
    - Implement exception handlers for FastAPI
    - Add retry logic for AWS service calls
    - _Requirements: 9.10_
  
  - [ ]* 12.3 Write property test for API error response format
    - **Property 14: API Error Response Format**
    - **Validates: Requirements 9.10**

- [ ] 13. Checkpoint - Backend API complete, test all endpoints
  - Run all unit tests and property tests
  - Test API endpoints manually with Postman or curl
  - Verify AWS service integrations work
  - Ensure database migrations are applied
  - Ask the user if questions arise

- [ ] 14. Implement frontend routing and layout
  - [ ] 14.1 Set up React Router
    - Configure routes: /, /farmer/login, /farmer/dashboard, /farmer/create-batch, /consumer/scan, /consumer/verification/:qrId
    - Create route guards for authenticated routes
    - _Requirements: 1.3, 1.4, 1.5_
  
  - [ ] 14.2 Create layout components
    - Create Header component with language switcher
    - Create Footer component
    - Create Layout wrapper component
    - _Requirements: 8.7_
  
  - [ ] 14.3 Implement theme configuration
    - Create Tailwind theme with green colors for Farmer Mode
    - Create Tailwind theme with blue colors for Consumer Mode
    - Create theme context provider
    - _Requirements: 1.2, 7.12_

- [ ] 15. Implement language switcher and translation context
  - [ ] 15.1 Create language context and provider
    - Implement language state management
    - Detect browser language on mount
    - Persist language selection to localStorage
    - _Requirements: 8.1, 8.2, 8.4_
  
  - [ ] 15.2 Create LanguageSwitcher component
    - Display dropdown with 10 language options
    - Handle language change
    - Show current language
    - _Requirements: 8.1, 8.7_
  
  - [ ] 15.3 Create translation utility functions
    - Implement useTranslation hook
    - Create translation key mapping
    - Integrate with backend /api/translate endpoint for dynamic content
    - _Requirements: 8.3, 8.5, 8.6_
  
  - [ ]* 15.4 Write property test for language preference persistence
    - **Property 12: Language Preference Persistence**
    - **Validates: Requirements 8.4**

- [ ] 16. Implement Mode Selection page
  - [ ] 16.1 Create ModeSelection component
    - Display two large cards (Farmer Mode green, Consumer Mode blue)
    - Add icons (tractor for farmer, shopping basket for consumer)
    - Handle card clicks to navigate
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5_
  
  - [ ] 16.2 Write unit tests for ModeSelection
    - Test component renders two cards
    - Test farmer card navigation
    - Test consumer card navigation
    - Test theme colors are applied
    - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [ ] 17. Implement Farmer Login page
  - [ ] 17.1 Create FarmerLogin component
    - Create phone input field (large, mobile-friendly)
    - Create OTP input field (large, mobile-friendly)
    - Create login button
    - Handle form submission
    - Call /api/auth/login endpoint
    - Store token in localStorage
    - Navigate to dashboard on success
    - Display error message on failure
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_
  
  - [ ] 17.2 Write unit tests for FarmerLogin
    - Test form renders correctly
    - Test successful login flow
    - Test failed login flow
    - Test error message display
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [ ] 18. Implement Farmer Dashboard page
  - [ ] 18.1 Create FarmerDashboard component
    - Fetch farmer's crop batches from backend
    - Display batch list with crop name, harvest date, safety score
    - Show QR code download button for completed batches
    - Add "Create New Batch" button
    - _Requirements: 1.3, 2.3_
  
  - [ ] 18.2 Create CropBatchCard component
    - Display batch summary
    - Show safety score gauge if available
    - Show QR code thumbnail if available
    - Add download QR button
    - _Requirements: 5.4_
  
  - [ ] 18.3 Write unit tests for FarmerDashboard
    - Test batch list renders
    - Test create batch button navigation
    - Test QR download functionality
    - _Requirements: 2.3, 5.4_

- [ ] 19. Implement Create Batch page - Image Upload and OCR
  - [ ] 19.1 Create ImageUpload component
    - Accept file input with camera capture option
    - Display image preview
    - Show upload progress
    - Call /api/image/upload endpoint
    - Display extracted data
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
  
  - [ ] 19.2 Create CreateBatchForm component
    - Add seed packet image upload section
    - Add crop images upload section (multiple)
    - Display extracted crop name and variety (editable)
    - Add farming method dropdown
    - Add harvest date picker
    - _Requirements: 3.1, 3.2, 3.5, 3.6, 3.7, 3.8_
  
  - [ ] 19.3 Write unit tests for image upload
    - Test file selection
    - Test image preview
    - Test OCR data display
    - Test manual editing of extracted data
    - _Requirements: 3.1, 3.5, 3.8_

- [ ] 20. Implement Create Batch page - Treatment Entry
  - [ ] 20.1 Create TreatmentEntry component
    - Add pesticide package image upload
    - Display extracted pesticide name and dosage (editable)
    - Add manual application date picker
    - Support multiple pesticides
    - Add fertilizer package image upload
    - Display extracted fertilizer name and quantity (editable)
    - Add manual application date picker
    - Support multiple fertilizers
    - _Requirements: 3.3, 3.4, 3.5, 3.6, 3.8_
  
  - [ ] 20.2 Implement form validation
    - Validate all required fields are filled
    - Enable "Analyze" button only when form is complete
    - Show validation errors
    - _Requirements: 3.6, 3.7, 3.11_
  
  - [ ] 20.3 Write unit tests for treatment entry
    - Test pesticide entry
    - Test fertilizer entry
    - Test form validation
    - Test analyze button state
    - _Requirements: 3.6, 3.7, 3.11_

- [ ] 21. Implement Create Batch page - AI Analysis and QR Generation
  - [ ] 21.1 Implement batch submission and AI analysis
    - Call /api/batch/create endpoint
    - Call /api/ai/analyze endpoint
    - Show loading state during analysis
    - Display analysis results (safety score, risk level, explanation)
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  
  - [ ] 21.2 Create SafetyScoreGauge component
    - Display circular gauge with score 0-100
    - Color code: green (71-100), yellow (41-70), red (0-40)
    - Show risk level label
    - Support small, medium, large sizes
    - _Requirements: 4.2, 4.3, 7.1, 12.5_
  
  - [ ] 21.3 Implement QR code generation
    - Call /api/qr/generate endpoint after analysis
    - Display generated QR code
    - Provide download button
    - Show public verification URL
    - _Requirements: 5.1, 5.2, 5.3, 5.4_
  
  - [ ] 21.4 Write unit tests for analysis and QR generation
    - Test AI analysis flow
    - Test safety gauge rendering
    - Test QR generation
    - Test QR download
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 5.1, 5.2, 5.3, 5.4_

- [ ] 22. Implement Consumer Scan page
  - [ ] 22.1 Create QRScanner component
    - Use html5-qrcode library
    - Auto-open camera on mount
    - Display scanner overlay with frame
    - Decode QR code
    - Extract QR ID from decoded data
    - Navigate to verification page with QR ID
    - _Requirements: 6.1, 6.2, 6.3, 6.5_
  
  - [ ] 22.2 Create ScanAnimation component
    - Display verification animation after successful scan
    - Show loading state
    - _Requirements: 6.4_
  
  - [ ] 22.3 Write unit tests for QR scanner
    - Test scanner component renders
    - Test QR decode handling
    - Test navigation after scan
    - _Requirements: 6.2, 6.3, 6.5_

- [ ] 23. Implement Consumer Verification page
  - [ ] 23.1 Create VerificationPage component
    - Fetch data from /api/public/{qr_id} endpoint
    - Display safety score gauge (large)
    - Display risk level badge
    - Display farmer information
    - Display harvest date
    - _Requirements: 6.6, 7.1, 7.2, 7.3, 7.4, 7.7_
  
  - [ ] 23.2 Create Timeline component
    - Display crop journey events vertically
    - Show planting, treatment, harvest events
    - Display dates and descriptions
    - _Requirements: 7.5, 12.6_
  
  - [ ] 23.3 Create TreatmentHistory component
    - Display pesticides table with name, dosage, application date
    - Display fertilizers table with name, quantity, application date
    - _Requirements: 7.6_
  
  - [ ] 23.4 Write unit tests for verification page
    - Test data fetching and display
    - Test safety gauge rendering
    - Test timeline rendering
    - Test treatment history rendering
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7_

- [ ] 24. Implement Consumer Verification page - AI Consumption Advice
  - [ ] 24.1 Create ConsumptionAdvice component
    - Add "Get AI Advice" button
    - Call /api/ai/consumption-advice endpoint with language parameter
    - Display cleaning instructions
    - Display safety tips
    - Display consumption recommendations
    - Support translation to selected language
    - _Requirements: 7.8, 7.9, 7.10, 7.11, 8.5_
  
  - [ ] 24.2 Write unit tests for consumption advice
    - Test advice fetching
    - Test advice display
    - Test translation
    - _Requirements: 7.8, 7.9, 7.10, 7.11_

- [ ] 25. Implement PWA configuration
  - [ ] 25.1 Create service worker
    - Cache static assets
    - Cache API responses for offline access
    - Implement cache-first strategy for images
    - Implement network-first strategy for API calls
    - _Requirements: 11.1, 11.3_
  
  - [ ] 25.2 Create app manifest
    - Set app name, icons, theme colors
    - Configure display mode to standalone
    - Set start URL
    - _Requirements: 11.4_
  
  - [ ] 25.3 Write unit tests for PWA
    - Test service worker registration
    - Test offline functionality
    - Test manifest configuration
    - _Requirements: 11.1, 11.3, 11.4_

- [ ] 26. Checkpoint - Frontend complete, test all user flows
  - Test mode selection flow
  - Test farmer login and dashboard
  - Test complete batch creation with OCR
  - Test AI analysis and QR generation
  - Test consumer QR scanning
  - Test verification page with all data
  - Test consumption advice
  - Test language switching
  - Test PWA installation
  - Ask the user if questions arise

- [ ] 27. Implement AWS Lambda deployment configuration
  - [ ] 27.1 Create Mangum adapter for FastAPI
    - Wrap FastAPI app with Mangum handler
    - Configure for AWS Lambda runtime
    - _Requirements: 10.1_
  
  - [ ] 27.2 Create Lambda deployment package
    - Create requirements.txt with all dependencies
    - Create Dockerfile for Lambda container image
    - Configure Lambda function settings (memory, timeout)
    - _Requirements: 10.1, 10.6_
  
  - [ ] 27.3 Create deployment script
    - Build Lambda deployment package
    - Upload to AWS Lambda
    - Configure environment variables
    - _Requirements: 10.6, 10.7_

- [ ] 28. Implement AWS infrastructure setup scripts
  - [ ] 28.1 Create S3 bucket setup script
    - Create S3 bucket for images and QR codes
    - Configure bucket CORS
    - Set bucket policy for public read access to QR codes
    - _Requirements: 10.3, 10.5_
  
  - [ ] 28.2 Create RDS setup script
    - Create RDS PostgreSQL instance
    - Configure security groups
    - Run database migrations
    - _Requirements: 10.4_
  
  - [ ] 28.3 Create API Gateway configuration
    - Create REST API
    - Configure routes to Lambda function
    - Enable CORS
    - Set up custom domain (optional)
    - _Requirements: 10.2, 10.8_
  
  - [ ] 28.4 Create CloudFront distribution for frontend
    - Create S3 bucket for frontend static files
    - Configure CloudFront distribution
    - Set up SSL certificate
    - Configure cache behaviors
    - _Requirements: 10.3_

- [ ] 29. Create deployment documentation and scripts
  - [ ] 29.1 Create deploy.sh script
    - Build frontend (npm run build)
    - Upload frontend to S3
    - Invalidate CloudFront cache
    - Build backend Lambda package
    - Deploy Lambda function
    - Update API Gateway
    - _Requirements: 10.7_
  
  - [ ] 29.2 Create environment setup documentation
    - Document all required AWS credentials
    - Document environment variables
    - Document deployment steps
    - Create troubleshooting guide
    - _Requirements: 10.6, 10.7_
  
  - [ ] 29.3 Create demo data seeding script
    - Create sample farmer accounts
    - Create sample crop batches
    - Generate sample QR codes
    - _Requirements: 2.2_

- [ ] 30. Final integration testing and deployment
  - [ ]* 30.1 Run complete integration test suite
    - Test farmer workflow end-to-end
    - Test consumer workflow end-to-end
    - Test multi-language functionality
    - Test AWS service integrations
    - Test error handling
  
  - [ ] 30.2 Deploy to AWS production environment
    - Run deployment script
    - Verify all services are running
    - Test production URLs
    - Monitor CloudWatch logs
    - _Requirements: 10.1, 10.2, 10.3, 10.4, 10.5, 10.6, 10.7, 10.8_
  
  - [ ] 30.3 Create README with setup and run instructions
    - Document local development setup
    - Document AWS deployment process
    - Document API endpoints
    - Document testing procedures
    - Include demo credentials

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints (tasks 13, 26) ensure incremental validation
- Property tests validate universal correctness properties (minimum 100 iterations each)
- Unit tests validate specific examples and edge cases
- AWS service integrations should be tested with mocked services locally before deploying
- All environment variables must be documented in .env.example files
- The platform must be fully deployable to AWS immediately after completion

