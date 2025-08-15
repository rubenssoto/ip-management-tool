# TICKET #2: Core Google Apps Script Backend Services

**Priority:** Critical | **Story Points:** 8 | **Estimated Time:** 8-12 hours

## Overview
Implement the complete backend infrastructure using Google Apps Script for authentication, data access, CRUD operations, and audit logging.

## File Structure to Create

### Google Apps Script Project: `IP-Allowlist-Manager`

Create a new Google Apps Script project and implement these files:

#### Required Files:
- `Code.gs` - Main entry point and core functions
- `DataService.gs` - Data access layer and CRUD operations
- `ValidationService.gs` - Input validation and business rules
- `AuditService.gs` - Audit logging functionality
- `IPService.gs` - IP detection and validation utilities

## Tasks Checklist

### ✅ Project Setup
- [ ] Create new Google Apps Script project: `IP-Allowlist-Manager`
- [ ] Link to the Google Sheets database created in Ticket #1
- [ ] Set up project properties and configuration
- [ ] Configure OAuth scopes and permissions

### ✅ Core Infrastructure (Code.gs)
- [ ] Implement `doGet(e)` function for web app entry point
- [ ] Implement `include(filename)` function for HTML templating
- [ ] Set up error handling and logging
- [ ] Configure web app properties

### ✅ Authentication & Authorization (Code.gs)
- [ ] Implement user authentication using Session.getActiveUser()
- [ ] Implement `isValidDomainUser(email)` function
- [ ] Implement `isAdmin(email)` function using Admins sheet
- [ ] Add domain validation rules

### ✅ Data Access Layer (DataService.gs)
- [ ] Implement connection to Google Sheets
- [ ] Create base CRUD operations with error handling
- [ ] Implement concurrency control with LockService
- [ ] Add data transformation utilities
- [ ] Implement composite key uniqueness validation

### ✅ IP Management Operations
- [ ] `createIPEntry(ipData)` - Create new IP entry
- [ ] `getIPEntries(userEmail, isAdmin)` - Get user or all IPs
- [ ] `updateIPEntry(id, updates, actor)` - Update existing entry
- [ ] `deleteIPEntry(id, actor)` - Delete/deactivate entry
- [ ] `validateUniqueIPPlatform(ip, platform, excludeId)` - Check uniqueness

### ✅ Platform Management
- [ ] `getPlatforms()` - Get all available platforms
- [ ] `addPlatform(name, description)` - Add new platform (admin only)
- [ ] `updatePlatform(oldName, newName, description)` - Update platform
- [ ] `deletePlatform(name)` - Remove platform (admin only)

### ✅ Validation Service (ValidationService.gs)
- [ ] `validateIPFormat(ip)` - IPv4/CIDR validation
- [ ] `validateEmail(email)` - Email format validation
- [ ] `validateDomain(email)` - Company domain validation
- [ ] `sanitizeInput(input)` - Input sanitization
- [ ] `validateIPEntry(entry)` - Complete entry validation

### ✅ Audit Logging (AuditService.gs)
- [ ] `logAction(action, actor, ip, platform, details)` - Log all changes
- [ ] `getAuditLog(filters)` - Retrieve audit entries
- [ ] Automatic timestamp handling (UTC)
- [ ] JSON serialization for details

### ✅ IP Detection Service (IPService.gs)
- [ ] `detectUserIP()` - Get client's public IP
- [ ] IP geolocation utilities (optional)
- [ ] IP format conversion utilities

## Implementation Requirements

### Code.gs Specifications
- Implement `doGet(e)` function as main web app entry point
- Use `Session.getActiveUser()` for authentication
- Validate users against company domain
- Check admin status using Admins sheet
- Return HTML template with user context
- Implement `include(filename)` function for HTML templating
- Add proper security headers (XFrame protection)
- Include error handling for all operations

### Configuration Requirements
- Define project configuration constants
- Set spreadsheet ID from Ticket #1
- Configure company domain for validation
- Define sheet names for consistent access
- Set timezone for audit logging

### API Endpoints to Implement
- `getMyIPs()` - Get current user's IP entries
- `getAllIPs()` - Get all IPs (admin only)
- `createIP(ipData)` - Create new IP entry
- `updateIP(id, updates)` - Update IP entry
- `deleteIP(id)` - Delete IP entry
- `getPlatforms()` - Get available platforms
- `detectMyIP()` - Detect user's current IP
- `getAuditLog(filters)` - Get audit history (admin only)

## Security Requirements

### Input Validation
- All user inputs must be sanitized
- Email addresses validated against company domain
- IP addresses validated for proper format
- Platform names validated against allowed list

### Authorization Checks
- Every API endpoint must verify user authentication
- Owner-only operations enforced for regular users
- Admin operations properly gated
- Audit trail for all data modifications

### Concurrency Control Requirements
- Use LockService.getDocumentLock() for write operations
- Implement proper lock acquisition with timeout (10 seconds)
- Always release locks in finally blocks
- Handle lock timeout errors gracefully
- Prevent data corruption during concurrent operations

## Error Handling Standards

### Client-Friendly Errors
- User-facing error messages
- Detailed logging for debugging
- Graceful degradation for failures
- Proper HTTP status codes

### Logging Requirements
- All operations logged with timestamps
- User actions tracked for audit
- Error details captured for debugging
- Performance metrics collected

## Testing Checklist

### Unit Testing
- [ ] User authentication and authorization work correctly
- [ ] Data validation catches all invalid inputs
- [ ] CRUD operations handle edge cases
- [ ] Audit logging captures all required data
- [ ] IP detection works reliably

### Integration Testing  
- [ ] Google Sheets integration works properly
- [ ] User permissions enforced correctly
- [ ] Concurrent operations handled safely
- [ ] Error scenarios handled gracefully
- [ ] Performance acceptable under load

## Definition of Done
- ✅ All Google Apps Script files created and implemented
- ✅ Authentication and authorization working
- ✅ All CRUD operations functional with validation
- ✅ Audit logging captures all data changes
- ✅ Concurrency control prevents data corruption
- ✅ Error handling provides user-friendly feedback
- ✅ IP detection service working reliably
- ✅ Unit tests passing for core functions
- ✅ Integration with Google Sheets validated
- ✅ Security requirements fully implemented

## Next Steps
Once backend is complete, move to Ticket #3 for frontend foundation development.