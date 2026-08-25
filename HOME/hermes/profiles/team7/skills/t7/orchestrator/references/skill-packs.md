# Technology Skill Packs

Inject the relevant skill pack into sub-agent delegation CONTEXT when target technology is identified.

## WordPress Skill Pack
When: wp-content, wp-admin, wp-login.php detected
- Check /wp-admin, /wp-login.php, /xmlrpc.php
- Enumerate plugins via /wp-content/plugins/ and wp-json/wp/v2/plugins
- Test xmlrpc.php for brute force amplification
- Check for known vulnerable plugins (wpscan --enumerate vp)
- Test REST API user enumeration: /wp-json/wp/v2/users

## AWS Skill Pack
When: IMDS, S3 URLs, CloudFront headers, AWS metadata detected
- Check IMDS at 169.254.169.254 (v1 and v2)
- Enumerate S3 buckets: target-name, target-name-prod, target-name-backup
- Check for exposed .aws/credentials or environment variables
- Test IAM role assumption from compromised EC2
- Check CloudFront, Lambda, API Gateway misconfigurations

## JWT Skill Pack
When: JWT tokens in cookies/headers detected
- Test algorithm confusion (RS256 -> HS256)
- Test none algorithm bypass
- Check for weak signing keys (jwt_tool)
- Test claim manipulation (role, sub, iss)
- Check token expiry enforcement
- Test JWK/JWKS injection

## Docker/Container Skill Pack
When: Docker socket, container indicators detected
- Check for Docker socket exposure (/var/run/docker.sock)
- Test container escape via mounted volumes
- Check capabilities (capsh --print)
- Test cgroup escape techniques
- Enumerate container network (172.17.0.0/16)

## API Skill Pack
When: REST API endpoints, GraphQL detected
- Test BOLA/IDOR on all resource endpoints
- Check for mass assignment on PUT/PATCH
- Test rate limiting and pagination abuse
- GraphQL: introspection query, batching attacks, nested query DoS
- Check for verbose error messages leaking internals

## .NET Skill Pack
When: ASP.NET indicators detected
- Test deserialization (ViewState, JSON.NET TypeNameHandling)
- Check web.config exposure
- Test IIS shortname enumeration
- Check for debug mode/trace.axd
- Test padding oracle on ViewState

## Java/Spring Skill Pack
When: Java/Spring indicators detected
- Test JNDI injection (Log4Shell pattern)
- Check actuator endpoints (/actuator/env, /actuator/heapdump)
- Test deserialization (Java serialized objects, XML external entities)
- Check for exposed management ports (JMX, RMI)
- Test Spring Expression Language injection

## Node.js Skill Pack
When: Node.js/Express indicators detected
- Test prototype pollution (POST body with __proto__)
- Check for SSRF via axios/node-fetch (internal service access)
- Test template injection (EJS, Pug, Handlebars)
- Check for exposed source maps (.js.map)
- Test event loop blocking for DoS

## Selection Logic

| Discovery | Skill Pack |
|-----------|------------|
| WordPress detected (wp-content, wp-admin) | WordPress |
| AWS metadata, S3 URLs, CloudFront headers | AWS |
| JWT tokens in cookies/headers | JWT |
| Docker socket, container indicators | Docker/Container |
| REST API endpoints, GraphQL | API |
| .NET/ASP.NET indicators | .NET |
| Java/Spring indicators | Java/Spring |
| Node.js/Express indicators | Node.js |
| User requests PDF/DOCX/PPTX/XLSX work | Document Processing |

## Document Processing Skill Pack
When: User requests PDF/DOCX/PPTX/XLSX operations
- Load the appropriate Hermes skill: `skill_view(name="pdf")`, `skill_view(name="docx")`, `skill_view(name="powerpoint")`, `skill_view(name="xlsx")`
- PDF: pypdf for basic ops, pdfplumber for text/tables, reportlab for creation
- DOCX: python-docx for creation/reading/editing
- PPTX: python-pptx for creation/reading/editing
- XLSX: openpyxl for read/write/edit, xlsxwriter for high-performance write-only
- Always validate output files after creation
