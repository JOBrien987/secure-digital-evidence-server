# Secure Digital Evidence Storage Server (Ubuntu + ZFS)

## Overview

This repository documents the architecture and implementation of a secure digital evidence storage server designed and deployed to support operational investigative and supervision work.

The system was built to solve a real-world problem: ensuring that digital evidence collected during agency operations can be stored, preserved, and verified with strong integrity protections.

The platform provides a centralized storage environment designed to protect digital evidence from corruption, unauthorized modification, and accidental loss while maintaining verifiable integrity.

This documentation focuses on the technical architecture and security controls used to implement the system. Sensitive operational details and agency-specific information are intentionally omitted.

---

## Operational Problem

Digital evidence collected during investigations and supervision activities must be preserved in a way that guarantees integrity and prevents unauthorized modification.

Many small agencies lack dedicated digital evidence infrastructure and instead rely on standard file servers, removable media, or ad-hoc storage solutions. These approaches introduce several risks:

- evidence corruption
- accidental deletion
- lack of integrity verification
- limited recovery options after hardware failure

To address these issues, a dedicated evidence storage platform was designed and implemented using resilient open-source technologies.

---

## Design Objectives

The system was built around four core objectives:

1. Preserve digital evidence integrity  
2. Prevent unauthorized modification or access  
3. Protect against hardware failure  
4. Provide reliable backup and recovery capability  

The architecture prioritizes integrity, redundancy, and operational simplicity.

---

## System Architecture

Evidence is transferred from an evidence workstation to a dedicated storage server where files are verified and stored within a redundant ZFS storage pool.
Evidence Workstation
│
│ Secure Transfer
▼
Ubuntu Evidence Server
│
├── ZFS RAID Storage Pool
│
├── Evidence Dataset
│
├── Hash Verification
│
└── Snapshot Backup System
│
▼
External Backup Drive

---

## Hardware Configuration

Operating System Drive  
500GB Crucial SATA SSD

Primary Storage  
5 hard drives configured in a ZFS RAIDZ storage pool

Backup Storage  
External drive used for snapshot replication

CPU  
[Insert CPU model]

RAM  
[Insert RAM amount]

The operating system runs on the SSD while the evidence data is stored on the redundant RAID storage pool.

---

## Operating System

Ubuntu Linux

Ubuntu was selected because it provides:

- stability in server environments
- native ZFS support
- extensive documentation
- compatibility with forensic tooling

---

## Storage Architecture

The evidence storage pool uses **ZFS RAIDZ**.

Example configuration command:
    sudo zpool create evidence raidz /dev/sdb /dev/sdc /dev/sdd /dev/sde /dev/sdf

ZFS provides several capabilities critical for evidence storage:

- end-to-end checksumming
- corruption detection
- redundant storage
- snapshot capability
- simplified storage management

These features make ZFS well suited for environments where data integrity is essential.

---

## Evidence Integrity Verification

All evidence files are verified using SHA-256 hashing.

Example verification command:
    sha256sum evidence_image.dd

Hash values are recorded when evidence is acquired and verified again after transfer to confirm that the file has not changed.

Integrity verification ensures that stored evidence can be validated later if required.

---

## Evidence Intake Workflow

Evidence ingestion follows a controlled workflow designed to preserve file integrity.

1. Evidence is acquired from the source device  
2. A SHA-256 hash is generated  
3. Evidence is transferred to the evidence server  
4. The hash is verified after transfer  
5. Evidence is stored within the ZFS dataset  

Example process:
    sha256sum original_image.dd
    cp original_image.dd /evidence/
    sha256sum /evidence/original_image.dd

Matching hashes confirm that the evidence file was transferred without alteration.

---

## Access Control

Access to evidence storage is restricted using Linux user permissions.

Example configuration:
    sudo adduser evidencetech
    sudo chown -R evidencetech:evidence /evidence
    chmod 750 /evidence

These controls enforce a least-privilege access model and limit the ability to modify stored evidence.

---

## Backup Strategy

ZFS snapshots are used to create point-in-time backups of stored evidence.

Example snapshot command:
    zfs snapshot evidence@daily

Snapshots protect against:

- accidental deletion
- data corruption
- malicious modification

Snapshots are periodically replicated to an external backup device.

Example replication command:
    zfs send evidence@daily | zfs receive backup/evidence

This ensures that evidence can be recovered even if the primary storage system fails.

---

## Security Controls

The platform incorporates multiple layers of protection designed to safeguard digital evidence.

| Control | Purpose |
|--------|--------|
ZFS Checksums | Detect data corruption |
RAIDZ Redundancy | Protect against drive failure |
SHA-256 Hashing | Verify evidence integrity |
Access Controls | Restrict unauthorized access |
ZFS Snapshots | Enable point-in-time recovery |
Offline Backup | Provide disaster recovery capability |

---

## Operational Benefits

The system provides several improvements over traditional file storage solutions:

- centralized evidence repository
- verifiable evidence integrity
- redundant storage protection
- recoverable backups
- controlled access to sensitive data

These features reduce operational risk and improve the reliability of evidence storage.

---

## Lessons Learned

Building a digital evidence storage system requires careful consideration of both technical architecture and operational workflow.

Key observations from the project include:

- ZFS significantly simplifies redundant storage management
- cryptographic hashing is essential for evidence integrity
- snapshot backups provide strong recovery capability
- access control must be carefully configured to prevent unauthorized modification

---

## Future Enhancements

Planned improvements include:

- automated hash verification scripts
- file access audit logging
- automated snapshot scheduling
- integration with digital forensic analysis tools

---

## Security Notice

This repository documents the technical architecture used to build a secure evidence storage system.

Operational procedures, internal network architecture, and agency-specific information have been intentionally excluded to protect system security.
