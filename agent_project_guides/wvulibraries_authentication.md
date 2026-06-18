# WVU Libraries Authentication — Domain Context Guide
**Last Updated**: June 18, 2026
**Populated By**: GitHub Copilot
**Status**: Production application guide — LDAP-based authentication system
**Source**: `Authentication` repository (WVU Libraries internal)

> This file provides domain context for any agent working on the Authentication application.
> `@file` this into your Continue session before starting any Authentication task.
> The Authentication system is a PHP-based LDAP authentication gateway for WVU Libraries services.
> See also: `wvulibraries_knapsack.md` for related WVU Libraries projects.

---

## What the Authentication System Is
The **WVU Libraries Authentication System** is a centralized LDAP authentication gateway that provides login and authorization services for WVU Libraries internal systems (specifically the `systems.lib.wvu.edu` domain). It handles staff, patron, and temporary account authentication against the WVU Active Directory (WVU-AD) via LDAP bindings, and manages institutional patron accounts through a MySQL database backend.

The system is the security gateway for:
- Library systems staff login
- Temporary patron account creation and validation
- Alumni account authentication
- Resident borrower account management
- General authentication for library internal tools

Key facts:
- **Production URL**: `https://systems.lib.wvu.edu` (main VM), Docker dev at `http://localhost:8080`
- **Main Login Endpoint**: `/engineIncludes/login.php`
- **Authentication Domain**: `wvu-ad.wvu.edu` (WVU Active Directory)
- **Admin Contact**: Library Systems Office
- **License**: WVU Libraries internal use
- **Stack**: PHP 8.3, Apache 2.4, MySQL 5.7, LDAP extension, EngineAPI template engine, Docker/Docker Compose
- **Deployment**: Dockerized single-container `php:8.3-apache` with PHP LDAP, MySQLi, and PDO extensions
- **Key Configuration**: LDAP server = `ldap://wvu-ad.wvu.edu`, bind format = `username@wvu-ad.wvu.edu`

---

## Repository / Project Structure
The repository uses a standard WVU Libraries PHP application layout with separate configuration, SQL schemas, and Docker orchestration files.

```
Authentication/
├── src/
│   ├── public_html/               # Web-accessible document root
│   │   ├── index.php              # Root landing page (redirects to auth)
│   │   ├── engineIncludes/        # Login/logout scripts and utilities
│   │   │   ├── login.php          # Main login form handler (EngineAPI routing)
│   │   │   ├── login-3.0.php      # Current login implementation (EngineAPI-based)
│   │   │   ├── logout.php         # Logout and session destroy
│   │   │   ├── login-3.0.bak_safe_save_121419.php  # Backup version
│   │   │   ├── headerIncludes.php # EngineAPI template includes
│   │   │   ├── CKEditor/          # Rich text editor (for admin forms)
│   │   │   ├── jquery/            # jQuery library
│   │   │   ├── aceEditor/         # Code editor
│   │   │   └── [other utilities]  # Download, email, form helpers
│   │   ├── authentication/        # Authentication module (ACL, temp accounts)
│   │   │   ├── acl.php            # Access control lists
│   │   │   ├── index.html         # ACL documentation
│   │   │   ├── index.php          # ACL management (staff only)
│   │   │   └── tempAccounts/      # Temporary patron account creation
│   │   │       ├── index.php      # Temp account list and LDAP test interface
│   │   │       ├── print.php      # Print temp account credentials
│   │   │       └── queryHelper.php # LDAP query utilities
│   │   ├── css/                   # Stylesheets
│   │   │   ├── 2012/              # 2012 design system
│   │   │   ├── 2013/              # 2013 design system
│   │   │   ├── 2023/              # Modern WVU design system
│   │   │   ├── bootstrap.css      # Bootstrap framework
│   │   │   ├── print.css          # Print styles
│   │   │   └── [other CSS]        # Legacy and custom styles
│   │   ├── javascript/            # JavaScript files
│   │   │   ├── 2012/              # 2012 JS (includes bootstrap.js)
│   │   │   ├── jquery-*.js        # jQuery versions
│   │   │   ├── modernizr.js       # HTML5 polyfills
│   │   │   └── [other JS]         # Utility scripts
│   │   ├── images/                # Static images and logos
│   │   ├── swipeLookup.php        # Card swipe lookup (staff tool)
│   │   ├── checkUser.php          # LDAP user check (staff query tool)
│   │   ├── alumni/                # Alumni account management
│   │   ├── residentBorrowers/     # Resident borrower management
│   │   ├── tempAccounts/          # Legacy temp accounts path
│   │   └── engineIncludes.php     # Master EngineAPI includes
│   └── phpincludes/               # PHP backend logic (not web-accessible)
│       ├── engineAPI/             # Template engine
│       │   ├── engine/            # Core EngineAPI framework
│       │   │   ├── engine.php     # Main EngineAPI class
│       │   │   ├── modules/       # EngineAPI modules
│       │   │   ├── login/         # Login handlers
│       │   │   │   ├── ldap.php   # LDAP login implementation
│       │   │   │   ├── mysql.php  # MySQL login alternative
│       │   │   │   └── [other]    # Other auth types
│       │   │   ├── accessControl/ # ACL and permission modules
│       │   │   ├── config/        # Configuration files
│       │   │   │   └── default.php # LDAP server config, domains, paths
│       │   │   └── [other engine modules]
│       │   └── template/          # Template system
│       │       └── systems.2013.2col/ # Two-column template design
│       │           ├── templateHeader.php   # Header includes (CSS/JS refs)
│       │           ├── templateFooter.php   # Footer
│       │           └── public_html/templateIncludes/ # Component includes
│       ├── lookupUserInfo.php     # User info lookup helper
│       ├── databaseConnectors/    # Database connection stubs
│       └── [other backend logic]
├── config/
│   ├── 000-default.conf           # Apache VirtualHost configuration
│   ├── httpd.conf                 # Apache httpd configuration
│   ├── php.ini                    # PHP configuration (extensions, limits)
│   ├── mysql-config.cnf           # MySQL client configuration
│   └── [other configs]
├── serverConfiguration/           # Server-level configs
│   ├── httpd.conf                 # Apache configuration
│   ├── php.ini                    # PHP settings
│   ├── database.lib.wvu.edu.remote.php  # DB connection (remote)
│   ├── defaultPrivate.php         # Private defaults
│   ├── envHelper.php              # Environment variable helper
│   ├── 3rdParty/                  # Third-party packages
│   │   ├── epel-release-6-8.noarch.rpm
│   │   └── remi-release-6.rpm
│   └── [other server configs]
├── SQLFiles/                      # Database schema files
│   ├── schema.sql                 # Core schema
│   ├── authentication.sql         # Authentication tables
│   ├── EngineAPI.sql              # EngineAPI schema
│   ├── setup-docker.sql           # Docker initialization SQL
│   └── [other SQL]
├── scripts/
│   ├── entrypoint.sh              # Docker container startup script
│   └── setup.sh                   # Setup/initialization script
├── env/
│   ├── env.auth                   # Production environment variables
│   └── env.dev.auth               # Development environment variables
├── docker-compose.yml             # Production Docker orchestration
├── docker-compose.dev.yml         # Development Docker orchestration
├── Dockerfile                     # Production Docker image
├── up.sh                          # Start containers script
├── down.sh                        # Stop containers script
├── data/
│   ├── logs/                      # Apache/PHP logs (persistent volume)
│   └── [other data volumes]
└── README.md                      # Project overview
```

---

## Authentication Flow & Key Components

### Main Login Flow (login.php)
1. User visits `https://systems.lib.wvu.edu/engineIncludes/login.php?page=/authentication/tempAccounts/index.php`
2. Form submits to same endpoint with POST (username + password)
3. EngineAPI routes to `$engine->login("ldap")` which calls `/phpincludes/engineAPI/engine/login/ldap.php`
4. `ldapLogin()` function:
   - Extracts LDAP config from `$engineVars['domains']['wvu-ad']`
   - Connects to `ldap://wvu-ad.wvu.edu`
   - Calls `securityUserCheck()` to bind as `username@wvu-ad.wvu.edu` with provided password
   - On success: LDAP search retrieves user groups (memberOf), sets `$_SESSION['groups']`, `$_SESSION['ou']`, `$_SESSION['username']`, `$_SESSION['authType']`
   - On failure: Returns FALSE, displays "Login Failed" message on page
5. On success, redirects to `$page` parameter or `$engineVars['WEBROOT']`

### LDAP Configuration (engine/config/default.php)
```php
$engineVars['domains']['wvu-ad']['ldapServer'] = "ldap://wvu-ad.wvu.edu";
$engineVars['domains']['wvu-ad']['ldapDomain'] = "wvu-ad.wvu.edu";
$engineVars['domains']['wvu-ad']['dn'] = "DC=wvu-ad,DC=wvu,DC=edu";
$engineVars['domains']['wvu-ad']['filter'] = "(|(sAMAccountName=%USERNAME%))";
$engineVars['domains']['wvu-ad']['attributes'] = array("memberof","displayname");
```

### Key Functions
- **ldapLogin()** — Main entry point for LDAP authentication
- **securityUserCheck()** — Performs LDAP bind (`@ldap_bind($ldapconn, $username."@wvu-ad.wvu.edu", $password)`)
- **ldapConnect()** — Establishes LDAP connection with protocol v3 and referral options
- **ldapDisconnect()** — Unbinds and closes LDAP connection
- **getGroups()** — Extracts group names from memberOf LDAP attribute
- **getGroup()** — Regex parser for DN format group extraction

---

## EngineAPI Template System
The application uses the **EngineAPI** template engine (internal WVU framework) for:
- Template loading and rendering
- Local variable management (CSS/JS URLs, page titles, etc.)
- CSRF token insertion
- File inclusion and recursion

### Template Lifecycle
1. `$engine->eTemplate("load","systems")` — Loads systems template
2. `$engine->eTemplate("load","systems.2013.2col")` — Loads two-column variant
3. `$engine->eTemplate("include","header")` — Renders header with CSS/JS refs
4. Page content rendered
5. `$engine->eTemplate("include","footer")` — Renders footer

### Local Variables Used
- `jsURL` — JavaScript directory URL (set to `https://systems.lib.wvu.edu/javascript/2012`)
- `cssURL` — CSS directory URL (set to `https://systems.lib.wvu.edu/css/2012`)
- `cssExt` — CSS file extension (`less` or `css`)
- `pageTitle` — Page heading
- Other custom variables set per page

---

## Database Schema
### Key Tables
- **tempAccounts** — Temporary patron accounts (username, password, created_date, status)
- **alumni** — Alumni patron records (uid, username, firstname, lastname, birthdate, status, proxy)
- **residentBorrowers** — Resident borrower accounts (uid, username, status, proxy)
- **accountUsernames** — Mapping of UIDs to usernames (for card swipe lookups)
- **Users** (EngineAPI) — System users and session management

---

## Docker & Deployment

### Dockerfile
- Base: `php:8.3-apache`
- Extensions: LDAP, MySQLi, PDO MySQL
- LDAP Configuration: `docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/`
- Apache config: Mounted from `./config/000-default.conf`
- Entrypoint: `./scripts/entrypoint.sh`

### Docker Compose (Dev)
```yaml
version: '3.4'
services:
  app:
    build: ./
    container_name: systems
    volumes:
      - ./env/env.dev.auth:/etc/.env
      - ./src/public_html:/home/systems/public_html
      - ./src/phpincludes:/home/systems/phpincludes
      - ./data/logs:/tmp/log
    env_file: './env/env.dev.auth'
    ports:
      - "8080:80"
    depends_on:
      db:
        condition: service_healthy
```

### Environment Variables
- `MYSQL_HOST` — Database hostname (`db` for Docker, `localhost` for VM)
- `MYSQL_USER` — Database username
- `MYSQL_PASSWORD` — Database password
- `MYSQL_DATABASE` — Database name (`authentication`)
- `LDAP_SERVER` — LDAP server URL (defaults to `ldap://wvu-ad.wvu.edu`)

---

## Workspace & Testing Protocols

### Static Assets & Symlink Issue
The application references Bootstrap CSS/JS via EngineAPI local variables pointing to `/css/2012/` and `/javascript/2012/`. These need to exist or be properly aliased:
- `bootstrap.js` should resolve to `/javascript/2012/bootstrap.js` or symlink
- `bootstrap.css` should resolve to `/css/2012/bootstrap.css` or symlink
- `print.css` should resolve to `/css/2012/print.css` or symlink
- `fonts.*` should resolve to `/css/2012/fonts.less` or equiv

The commented-out symlink lines in `scripts/entrypoint.sh` may need uncommentation if static asset 404s occur.

### LDAP Connectivity
- Network access to `ldap://wvu-ad.wvu.edu` is required (production VM has this; Docker containers may not without network bridging)
- PHP LDAP extension must be installed and enabled
- Kernel patches or network configuration changes can affect LDAP binding

### Common Issues & Troubleshooting
| Issue | Cause | Resolution |
|-------|-------|-----------|
| "Login Failed" on valid credentials | LDAP server unreachable or bind failure | Check LDAP server URL in config; test network connectivity to ldap://wvu-ad.wvu.edu |
| Missing static assets (CSS/JS 404) | Bootstrap/print.css not symlinked | Uncomment symlink lines in scripts/entrypoint.sh and restart container |
| MySQL connection failure | DB hostname misconfigured | Verify `$engineVars['mysql']['server']` points to correct host (db for Docker, localhost for VM) |
| Session not persisting | PHP session directory permissions | Check /tmp permissions inside container |

---

## What NOT To Do
- **Never hardcode LDAP server URLs**: Use `$engineVars['domains']['wvu-ad']['ldapServer']` — configuration is centralized in `engine/config/default.php`
- **Never bypass EngineAPI's localVars system**: Always use `$engine->localVars()` to set/get template variables; do not directly manipulate template includes
- **Never assume LDAP is accessible from Docker**: Network access to WVU-AD from containers is not guaranteed without explicit configuration
- **Never modify EngineAPI core files without understanding module dependencies**: EngineAPI is a shared framework; changes can break other WVU Libraries applications
- **Never commit environment variable files (env.auth, env.dev.auth)**: These contain secrets; use .env.example or documentation instead
- **Never assume static assets are present in git**: Bootstrap and jQuery files may be symlinked or loaded from external CDN; verify configuration before implementing paths
