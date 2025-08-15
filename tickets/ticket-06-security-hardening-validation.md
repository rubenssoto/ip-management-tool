# TICKET #6: Security Hardening & Validation

**Priority:** Critical | **Story Points:** 4 | **Estimated Time:** 6-8 hours

## Overview
Implement comprehensive security measures including input validation, XSS protection, CSRF protection, rate limiting, and secure session handling to ensure the application meets enterprise security standards.

## Security Requirements

### Input Validation & Sanitization
- **Server-side validation** for all user inputs
- **Client-side validation** for immediate feedback
- **XSS prevention** through proper output encoding
- **SQL injection prevention** (Google Sheets context)
- **Data type validation** at API boundaries

### Authentication & Authorization
- **Domain-based access control** enforcement
- **Role-based permissions** (user vs admin)
- **Session security** and timeout handling
- **API endpoint protection** with proper auth checks

### Data Protection
- **Sensitive data handling** (no credentials in client code)
- **Audit trail protection** (append-only, immutable)
- **Rate limiting** to prevent abuse
- **Error information disclosure** prevention

## Tasks Checklist

### ✅ Input Validation Framework
- [ ] Create comprehensive validation library
- [ ] Implement IPv4/CIDR validation with security checks
- [ ] Add email validation with domain restrictions
- [ ] Create text sanitization functions
- [ ] Implement length and format constraints
- [ ] Add special character filtering

### ✅ XSS Protection
- [ ] Implement HTML output encoding
- [ ] Sanitize user-generated content display
- [ ] Use safe DOM manipulation methods
- [ ] Validate and encode JSON responses
- [ ] Implement Content Security Policy headers
- [ ] Add X-Frame-Options protection

### ✅ Authentication Security
- [ ] Strengthen domain validation checks
- [ ] Implement session timeout handling
- [ ] Add user session verification
- [ ] Create secure admin role checking
- [ ] Implement logout functionality
- [ ] Add session invalidation on security events

### ✅ API Security
- [ ] Add request size limits
- [ ] Implement rate limiting per user
- [ ] Create API input validation middleware
- [ ] Add proper error handling without information disclosure
- [ ] Implement request logging for security monitoring
- [ ] Add CSRF protection where applicable

### ✅ Data Security
- [ ] Encrypt sensitive configuration data
- [ ] Implement secure property storage
- [ ] Add audit log integrity checks
- [ ] Create secure data transmission
- [ ] Implement data access logging
- [ ] Add backup security considerations

### ✅ Security Headers & Configuration
- [ ] Configure security headers in Apps Script
- [ ] Set up proper CORS policies
- [ ] Implement secure cookie settings
- [ ] Add security monitoring hooks
- [ ] Create security incident logging
- [ ] Document security configuration

## Detailed Security Implementation

### Input Validation Library
```javascript
// SecurityService.gs
class SecurityService {
    
    /**
     * Comprehensive input validation
     */
    static validateInput(input, type, options = {}) {
        if (!input && options.required !== false) {
            throw new SecurityError('Input is required');
        }

        switch (type) {
            case 'ip':
                return this.validateIP(input);
            case 'email':
                return this.validateEmail(input, options.domain);
            case 'text':
                return this.validateText(input, options);
            case 'platform':
                return this.validatePlatform(input);
            default:
                throw new SecurityError('Unknown validation type');
        }
    }

    /**
     * IP address validation with security considerations
     */
    static validateIP(ip) {
        if (!ip || typeof ip !== 'string') {
            throw new SecurityError('Invalid IP format');
        }

        // Remove any potential injection attempts
        const cleanIP = ip.trim().replace(/[^\d\.\/]/g, '');
        
        // IPv4 validation
        const ipv4Regex = /^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
        const cidrRegex = /^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\/(3[0-2]|[12]?[0-9])$/;
        
        if (!ipv4Regex.test(cleanIP) && !cidrRegex.test(cleanIP)) {
            throw new SecurityError('Invalid IPv4 or CIDR format');
        }

        // Check for private/reserved ranges that might be suspicious
        this.checkIPSecurity(cleanIP);
        
        return cleanIP;
    }

    /**
     * Email validation with domain restrictions
     */
    static validateEmail(email, requiredDomain) {
        if (!email || typeof email !== 'string') {
            throw new SecurityError('Invalid email format');
        }

        const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
        if (!emailRegex.test(email)) {
            throw new SecurityError('Invalid email format');
        }

        if (requiredDomain && !email.toLowerCase().endsWith(`@${requiredDomain.toLowerCase()}`)) {
            throw new SecurityError(`Email must be from ${requiredDomain} domain`);
        }

        return email.toLowerCase();
    }

    /**
     * Text validation and sanitization
     */
    static validateText(text, options = {}) {
        if (!text) {
            return options.allowEmpty ? '' : null;
        }

        if (typeof text !== 'string') {
            throw new SecurityError('Input must be a string');
        }

        // Length validation
        if (options.maxLength && text.length > options.maxLength) {
            throw new SecurityError(`Text exceeds maximum length of ${options.maxLength}`);
        }

        if (options.minLength && text.length < options.minLength) {
            throw new SecurityError(`Text must be at least ${options.minLength} characters`);
        }

        // Sanitize potentially dangerous content
        let sanitized = text
            .replace(/<script\b[^<]*(?:(?!<\/script>)<[^<]*)*<\/script>/gi, '') // Remove scripts
            .replace(/<iframe\b[^<]*(?:(?!<\/iframe>)<[^<]*)*<\/iframe>/gi, '') // Remove iframes
            .replace(/javascript:/gi, '') // Remove javascript: URLs
            .replace(/on\w+\s*=/gi, ''); // Remove event handlers

        if (options.allowHTML !== true) {
            sanitized = sanitized.replace(/<[^>]*>/g, ''); // Strip HTML tags
        }

        return sanitized.trim();
    }

    /**
     * Check for suspicious IP ranges
     */
    static checkIPSecurity(ip) {
        const suspiciousRanges = [
            /^0\./, // Invalid range
            /^127\./, // Loopback (might be suspicious in some contexts)
            /^169\.254\./, // Link-local
            /^224\./, // Multicast
            /^240\./ // Reserved
        ];

        for (const range of suspiciousRanges) {
            if (range.test(ip)) {
                console.warn(`Potentially suspicious IP detected: ${ip}`);
                // Log but don't block - business decision
            }
        }
    }
}

/**
 * Custom security error class
 */
class SecurityError extends Error {
    constructor(message) {
        super(message);
        this.name = 'SecurityError';
    }
}
```

### XSS Protection Implementation
**Requirement:** Implement a comprehensive XSS protection service with the following specifications:
- XSSProtection class with methods for different encoding contexts
- HTML encoding method that converts dangerous characters (&, <, >, ", ', /) to HTML entities
- JavaScript encoding method that escapes characters for safe inclusion in JS strings
- Safe JSON stringification that encodes string values to prevent XSS in JSON output
- JSON validation with size limits and format checking
- Recursive object sanitization that cleans all string properties in nested objects and arrays
- Integration with SecurityService for text validation
- Protection against script injection in object properties and values
- Size limits on JSON input to prevent denial of service attacks
- Proper error handling with SecurityError for invalid JSON
- Support for sanitizing complex data structures with mixed types

### Rate Limiting Implementation
**Requirement:** Implement a rate limiting service to prevent API abuse with the following specifications:
- RateLimiter class using Google Apps Script CacheService for request tracking
- checkRateLimit() method that tracks requests per user per action with configurable limits
- Cache key generation using action type and user email for granular control
- Configurable time windows (default 60 minutes) and request limits (default 100)
- Automatic counter increment and expiration handling
- Warning logs when users approach rate limits (80% threshold)
- SecurityError throwing when limits are exceeded
- Graceful degradation when cache service fails (log error but don't block)
- withRateLimit() wrapper method for easy integration with API endpoints
- Per-action rate limiting to allow different limits for different operations
- Integration with audit logging for rate limit violations
- Cache expiration handling to reset counters after time window

### Secure API Wrapper
**Requirement:** Implement a security wrapper for all API endpoints with the following specifications:
- SecureAPIWrapper class that wraps all API functions with security checks
- Authentication verification using Session.getActiveUser() from Google Apps Script
- Domain validation to ensure users are from authorized domain
- Rate limiting integration for each API operation
- Input sanitization for all function arguments (strings and objects)
- Audit logging for all API calls with operation, user, arguments, and success status
- Standardized response format with success/failure, data/error, and timestamp
- Security event logging for failed authentication or authorization attempts
- Safe error message handling that doesn't expose internal system details
- Exception handling that differentiates between security errors and system errors
- Operation-specific security policies and logging
- Response formatting that's consistent across all API endpoints

### Security Configuration
**Requirement:** Implement centralized security configuration with the following specifications:
- SECURITY_CONFIG object containing all security-related settings
- Domain restriction configuration with company domain specification
- Rate limit configuration with operation-specific limits (create_ip: 20/hour, update_ip: 50/hour, etc.)
- Input size limits for different field types to prevent oversized input attacks
- Session timeout configuration (8 hours default)
- Security headers configuration including X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, Referrer-Policy
- applySecurityHeaders() function that adds security headers to HTML output
- Integration with Google Apps Script HtmlOutput.addMetaTag() method
- Configurable values that can be easily updated for different environments
- Documentation of each security setting and its purpose
- Validation of configuration values on application startup

### Client-Side Security Measures
```javascript
// client-security.js (in app.js.html)
class ClientSecurity {
    
    /**
     * Validate form inputs before submission
     */
    static validateForm(formData) {
        const errors = [];

        // IP validation
        if (!this.isValidIP(formData.ip)) {
            errors.push('Please enter a valid IPv4 address or CIDR range');
        }

        // Required fields
        if (!formData.platform) {
            errors.push('Platform selection is required');
        }

        // Length validation
        if (formData.label && formData.label.length > 100) {
            errors.push('Label must be 100 characters or less');
        }

        if (formData.notes && formData.notes.length > 500) {
            errors.push('Notes must be 500 characters or less');
        }

        return errors;
    }

    /**
     * Sanitize display content
     */
    static sanitizeForDisplay(text) {
        if (!text) return '';
        
        const div = document.createElement('div');
        div.textContent = text;
        return div.innerHTML;
    }

    /**
     * Safe HTML insertion
     */
    static safeInsertHTML(element, content) {
        // Use textContent for user-generated content
        element.textContent = content;
    }

    /**
     * Validate IP format
     */
    static isValidIP(ip) {
        const ipv4Regex = /^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/;
        const cidrRegex = /^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\/(3[0-2]|[12]?[0-9])$/;
        
        return ipv4Regex.test(ip) || cidrRegex.test(ip);
    }
}
```

## Security Testing Checklist

### Input Validation Tests
- [ ] Test XSS payloads in all input fields
- [ ] Test SQL injection attempts
- [ ] Test buffer overflow attempts
- [ ] Test invalid IP formats
- [ ] Test email injection attempts
- [ ] Test file upload security (if applicable)

### Authentication Tests
- [ ] Test domain bypass attempts
- [ ] Test session hijacking scenarios
- [ ] Test role escalation attempts
- [ ] Test logout functionality
- [ ] Test concurrent session handling

### API Security Tests
- [ ] Test rate limiting enforcement
- [ ] Test unauthorized API access
- [ ] Test malformed request handling
- [ ] Test large payload handling
- [ ] Test concurrent request handling

## Security Monitoring

### Logging Requirements
- [ ] Log all authentication attempts
- [ ] Log all authorization failures
- [ ] Log all input validation failures
- [ ] Log all rate limit violations
- [ ] Log all suspicious activities

### Alerting Setup
- [ ] Set up alerts for repeated security violations
- [ ] Monitor for unusual access patterns
- [ ] Alert on privilege escalation attempts
- [ ] Monitor for data export activities
- [ ] Track failed authentication attempts

## Definition of Done
- ✅ All input validation implemented with security focus
- ✅ XSS protection applied to all user-generated content
- ✅ Rate limiting prevents abuse of all endpoints
- ✅ Authentication and authorization properly secured
- ✅ Error handling doesn't expose sensitive information
- ✅ Security headers configured correctly
- ✅ Audit logging captures all security events
- ✅ Client-side validation provides immediate feedback
- ✅ Security testing completed without vulnerabilities
- ✅ Security monitoring and alerting configured
- ✅ Security documentation completed
- ✅ Code review completed with security focus

## Next Steps
Once security hardening is complete, move to Ticket #7 for error handling and user experience improvements.