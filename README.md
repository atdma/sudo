# Sudo - Credential Interception Version

## WARNING

**This is a vulnerable version of sudo modified for security research and educational purposes only.**

This version of sudo has been modified to intercept and log user credentials (username and password) to `/tmp/sudo.txt` during authentication. **DO NOT USE THIS IN PRODUCTION OR ON ANY SYSTEM WHERE SECURITY MATTERS.**

## Vulnerability Description

This modified sudo version captures user credentials during the authentication process and writes them to `/tmp/sudo.txt` in the following format:

```
username:password
```

Each authentication attempt appends a new line to the file. The credentials are captured immediately after the password is entered and before authentication verification occurs.

## Implementation Details

The credential interception is implemented in:
- **File**: `plugins/sudoers/auth/sudo_auth.c`
- **Function**: `log_credentials()` - Helper function that writes credentials to `/tmp/sudo.txt`
- **Location**: Called from `verify_user()` function after `auth_getpass()` successfully retrieves the password

The implementation:
- Captures the username from `ctx->user.name` or falls back to `ctx->user.pw->pw_name`
- Captures the password immediately after it's obtained from the user
- Writes to `/tmp/sudo.txt` in append mode (preserves previous attempts)
- Fails silently if logging fails (does not interrupt authentication flow)

## Building and Installation

### Prerequisites

- POSIX-compliant operating system (Linux, BSD, Unix)
- C compiler (ISO C99 or higher)
- Standard build tools (make, ar, ranlib)

### Build Steps

1. **Configure the build:**
   ```bash
   ./configure
   ```

2. **Compile:**
   ```bash
   make
   ```

3. **Install (as root):**
   ```bash
   sudo make install
   ```
   Or if already root:
   ```bash
   make install
   ```

## Testing the Vulnerability

After installation, test the credential interception:

1. Run sudo with a command that requires authentication:
   ```bash
   sudo whoami
   ```

2. Enter your password when prompted

3. Check `/tmp/sudo.txt` for logged credentials:
   ```bash
   cat /tmp/sudo.txt
   ```

You should see output like:
```
yourusername:yourpassword
```

## Security Implications

This vulnerability demonstrates:
- **Credential Theft**: Passwords are captured in plaintext
- **Persistent Logging**: Credentials are stored on disk
- **No User Awareness**: The interception is completely transparent
- **File Location**: Credentials are stored in `/tmp/` which may be world-readable

## Use Cases

This vulnerable version is intended for:
- Security research and education
- Penetration testing training
- Demonstrating credential interception vulnerabilities
- Security awareness training

## Legal and Ethical Notice

**This software is provided for educational and research purposes only. Unauthorized use of this software to intercept credentials on systems you do not own or have explicit permission to test is illegal and unethical.**

Always ensure you have proper authorization before:
- Installing this on any system
- Testing credential interception
- Using this for security research

## Removal

To remove this vulnerable version:

1. Uninstall the modified sudo:
   ```bash
   sudo make uninstall
   ```
   Or manually remove the installed binaries

2. Remove the credential log file:
   ```bash
   rm -f /tmp/sudo.txt
   ```

3. Reinstall the official, secure version of sudo from your distribution's package manager or from the official sudo project.

## Original Sudo Project

This is based on the sudo project by Todd C. Miller and contributors.

For the official, secure version of sudo, visit:
- Website: https://www.sudo.ws/
- Source: https://github.com/sudo-project/sudo

## License

This modified version retains the original ISC-style license. See LICENSE.md for details.

