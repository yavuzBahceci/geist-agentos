## Validation best practices

- **Validate at Trust Boundary**: Always validate at the trust boundary (where untrusted data enters the system); never trust validation performed in untrusted environments alone for security or data integrity
- **User Experience Validation**: Use validation in user-facing layers to provide immediate feedback, but always duplicate checks at the trust boundary
- **Fail Early**: Validate input as early as possible and reject invalid data before processing
- **Specific Error Messages**: Provide clear, field-specific error messages that help users correct their input
- **Allowlists Over Blocklists**: When possible, define what is allowed rather than trying to block everything that's not
- **Type and Format Validation**: Check data types, formats, ranges, and required fields systematically
- **Sanitize Input**: Sanitize user input to prevent injection attacks (data injection, code injection, command injection)
- **Business Rule Validation**: Validate business rules (e.g., sufficient balance, valid dates) at the appropriate application layer
- **Consistent Validation**: Apply validation consistently across all entry points (user interfaces, service interfaces, background processes)
