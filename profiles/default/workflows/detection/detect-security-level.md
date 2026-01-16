# Workflow: Detect Security Level

## Purpose

Check for security-related dependencies, secrets management patterns, and authentication configurations to determine the project's security level (low, moderate, high).

## Outputs

Sets the following variables:
- `DETECTED_SECURITY_LEVEL` - low, moderate, or high
- `SECURITY_INDICATORS` - Comma-separated list of detected security features
- `HAS_AUTH` - true/false
- `HAS_SECRETS_MANAGEMENT` - true/false
- `SECURITY_CONFIDENCE` - Confidence score (0.0 - 1.0)

---

## Detection Logic

### Step 1: Initialize

```bash
SECURITY_SCORE=0
SECURITY_INDICATORS=""
HAS_AUTH="false"
HAS_SECRETS_MANAGEMENT="false"
SECURITY_CONFIDENCE="0.70"
```

### Step 2: Check for Authentication Dependencies

```bash
echo "   Checking for authentication..."

# Node.js auth libraries
if [ -f "package.json" ]; then
    DEPS=$(cat package.json 2>/dev/null)
    
    # Auth libraries
    if echo "$DEPS" | grep -qE '"(passport|@auth0|firebase-admin|next-auth|lucia|clerk)"'; then
        HAS_AUTH="true"
        SECURITY_SCORE=$((SECURITY_SCORE + 3))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}auth-library,"
        echo "   ✓ Authentication library detected"
    fi
    
    # Password hashing
    if echo "$DEPS" | grep -qE '"(bcrypt|argon2|scrypt)"'; then
        SECURITY_SCORE=$((SECURITY_SCORE + 2))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}password-hashing,"
        echo "   ✓ Password hashing library detected"
    fi
    
    # JWT/Session
    if echo "$DEPS" | grep -qE '"(jsonwebtoken|jose|iron-session)"'; then
        SECURITY_SCORE=$((SECURITY_SCORE + 1))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}jwt-sessions,"
    fi
    
    # OAuth
    if echo "$DEPS" | grep -qE '"(oauth|passport-oauth|passport-google|passport-github)"'; then
        HAS_AUTH="true"
        SECURITY_SCORE=$((SECURITY_SCORE + 2))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}oauth,"
        echo "   ✓ OAuth integration detected"
    fi
fi

# Rust auth
if [ -f "Cargo.toml" ]; then
    CARGO=$(cat Cargo.toml 2>/dev/null)
    
    if echo "$CARGO" | grep -qE '(argon2|bcrypt|jsonwebtoken|oauth2)'; then
        HAS_AUTH="true"
        SECURITY_SCORE=$((SECURITY_SCORE + 3))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}rust-auth,"
    fi
fi

# Python auth
if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
    PYDEPS=""
    [ -f "requirements.txt" ] && PYDEPS="$PYDEPS $(cat requirements.txt)"
    [ -f "pyproject.toml" ] && PYDEPS="$PYDEPS $(cat pyproject.toml)"
    
    if echo "$PYDEPS" | grep -qiE '(passlib|bcrypt|python-jose|authlib|django-allauth)'; then
        HAS_AUTH="true"
        SECURITY_SCORE=$((SECURITY_SCORE + 3))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}python-auth,"
    fi
fi
```

### Step 3: Check for Secrets Management

```bash
echo "   Checking for secrets management..."

# Environment files
if [ -f ".env.example" ] || [ -f ".env.sample" ] || [ -f ".env.template" ]; then
    HAS_SECRETS_MANAGEMENT="true"
    SECURITY_SCORE=$((SECURITY_SCORE + 1))
    SECURITY_INDICATORS="${SECURITY_INDICATORS}env-files,"
    echo "   ✓ Environment file template detected"
fi

# Secrets in docker-compose
if [ -f "docker-compose.yml" ] || [ -f "docker-compose.yaml" ]; then
    DC_CONTENT=$(cat docker-compose.yml docker-compose.yaml 2>/dev/null)
    
    if echo "$DC_CONTENT" | grep -qE '(secrets:|vault)'; then
        HAS_SECRETS_MANAGEMENT="true"
        SECURITY_SCORE=$((SECURITY_SCORE + 2))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}docker-secrets,"
        echo "   ✓ Docker secrets detected"
    fi
fi

# Vault integration
if grep -rq "vault" . --include="*.yml" --include="*.yaml" --include="*.json" 2>/dev/null | head -1 | grep -q vault; then
    HAS_SECRETS_MANAGEMENT="true"
    SECURITY_SCORE=$((SECURITY_SCORE + 3))
    SECURITY_INDICATORS="${SECURITY_INDICATORS}vault,"
    echo "   ✓ HashiCorp Vault detected"
fi

# AWS Secrets Manager
if grep -rq "secretsmanager\|SecretsManager" . --include="*.ts" --include="*.js" --include="*.py" 2>/dev/null | head -1 | grep -q secret; then
    HAS_SECRETS_MANAGEMENT="true"
    SECURITY_SCORE=$((SECURITY_SCORE + 3))
    SECURITY_INDICATORS="${SECURITY_INDICATORS}aws-secrets,"
    echo "   ✓ AWS Secrets Manager detected"
fi
```

### Step 4: Check for Security Headers/Middleware

```bash
echo "   Checking for security middleware..."

# Helmet (Node.js security headers)
if [ -f "package.json" ]; then
    if grep -q '"helmet"' package.json 2>/dev/null; then
        SECURITY_SCORE=$((SECURITY_SCORE + 1))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}helmet,"
        echo "   ✓ Helmet security headers detected"
    fi
    
    # Rate limiting
    if grep -qE '"(express-rate-limit|rate-limiter-flexible)"' package.json 2>/dev/null; then
        SECURITY_SCORE=$((SECURITY_SCORE + 1))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}rate-limiting,"
        echo "   ✓ Rate limiting detected"
    fi
    
    # CORS
    if grep -q '"cors"' package.json 2>/dev/null; then
        SECURITY_INDICATORS="${SECURITY_INDICATORS}cors-config,"
    fi
fi
```

### Step 5: Check for Encryption

```bash
echo "   Checking for encryption..."

# Crypto libraries
if [ -f "package.json" ]; then
    if grep -qE '"(crypto-js|node-forge|tweetnacl)"' package.json 2>/dev/null; then
        SECURITY_SCORE=$((SECURITY_SCORE + 2))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}encryption,"
        echo "   ✓ Encryption library detected"
    fi
fi

# TLS/SSL configs
if [ -f "nginx.conf" ] || [ -d "ssl" ] || [ -d "certs" ]; then
    SECURITY_SCORE=$((SECURITY_SCORE + 1))
    SECURITY_INDICATORS="${SECURITY_INDICATORS}ssl-config,"
    echo "   ✓ SSL/TLS configuration detected"
fi
```

### Step 6: Check for Open Source Indicators (Lower Security Needs)

```bash
# Open source projects may have lower security needs
if [ -f "LICENSE" ]; then
    LICENSE_TYPE=$(head -5 LICENSE 2>/dev/null)
    
    if echo "$LICENSE_TYPE" | grep -qiE '(MIT|Apache|GPL|BSD|ISC)'; then
        # Open source - reduce security expectation slightly
        SECURITY_SCORE=$((SECURITY_SCORE - 1))
        SECURITY_INDICATORS="${SECURITY_INDICATORS}open-source,"
    fi
fi
```

### Step 7: Calculate Security Level

```bash
# Normalize score
[ $SECURITY_SCORE -lt 0 ] && SECURITY_SCORE=0

# Determine level
if [ $SECURITY_SCORE -ge 5 ]; then
    DETECTED_SECURITY_LEVEL="high"
    SECURITY_CONFIDENCE="0.90"
elif [ $SECURITY_SCORE -ge 2 ]; then
    DETECTED_SECURITY_LEVEL="moderate"
    SECURITY_CONFIDENCE="0.80"
else
    DETECTED_SECURITY_LEVEL="low"
    SECURITY_CONFIDENCE="0.70"
fi

# Clean up indicators string
SECURITY_INDICATORS=$(echo "$SECURITY_INDICATORS" | sed 's/,$//')

echo ""
echo "   Security Detection Summary:"
echo "   - Level: $DETECTED_SECURITY_LEVEL"
echo "   - Score: $SECURITY_SCORE"
echo "   - Has Auth: $HAS_AUTH"
echo "   - Has Secrets Management: $HAS_SECRETS_MANAGEMENT"
echo "   - Indicators: $SECURITY_INDICATORS"
```

---

## Security Level Guidelines

| Level | Score | Indicators |
|-------|-------|------------|
| **High** | 5+ | Auth + secrets management + encryption |
| **Moderate** | 2-4 | Some auth or env file management |
| **Low** | 0-1 | No security indicators, likely open source |

---

## Important Constraints

- Must not fail if files are missing
- Should not scan inside node_modules, vendor, etc.
- Open source projects default to lower security expectations
- Confidence increases with more indicators found
