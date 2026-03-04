flowchart TB

A[Evidence Workstation] -->|Secure Transfer| B[Ubuntu Evidence Server]

B --> C[ZFS Storage Pool]
C --> D[RAIDZ Disk Array]

C --> E[Evidence Dataset]

E --> F[SHA256 Hash Verification]

C --> G[ZFS Snapshot System]

G --> H[External Backup Drive]
