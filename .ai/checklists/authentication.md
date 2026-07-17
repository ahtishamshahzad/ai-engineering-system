# Checklist: Authentication

> Verifiable items for authentication (identity). Not authorization — that is separate.

## Pass Criteria

- [ ] Credentials stored only as strong adaptive hashes (bcrypt/argon2); never plaintext/fast-hash; never logged (`../skills/backend/backend-authentication`).
- [ ] Session vs JWT chosen on requirements; browsers use httpOnly/secure/sameSite cookies (not localStorage tokens).
- [ ] Token lifecycle: expiry, refresh rotation with reuse detection, **revocation** (logout / logout-all / password-change invalidates).
- [ ] Account flows safe: signup + verification; password reset (single-use, expiring); OTP (expiring, attempt-limited); MFA secrets protected.
- [ ] Responses **do not reveal account existence** (no enumeration) on login/reset.
- [ ] Public auth endpoints protected by **rate limiting + CAPTCHA** (abuse prevention is distinct from authN).
- [ ] Negative tests: wrong password, expired/tampered token, reused refresh token, reused reset token.

## Fail / Stop

- Plaintext/fast-hash passwords; unrevocable long-lived tokens; enumeration; reset links that don't expire.

## Related

Skills: `../skills/backend/backend-authentication`, `../skills/security/authentication-security`. Reference: `../references/backend/auth/`.
