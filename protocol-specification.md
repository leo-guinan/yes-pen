# Yes Pen Protocol Specification v0.1

*Draft specification for consent-first AI training data collection*

**Status:** Draft  
**Version:** 0.1.0  
**Last Updated:** [Date]  
**Authors:** [Your name], Genesis Partners (TBD)

## Abstract

The Yes Pen Protocol defines a standard for consensual collection, validation, and publication of conversational data for AI training. This protocol ensures all participants maintain sovereignty over their contributions while enabling collective ownership of resulting datasets and models.

## 1. Core Principles

### 1.1 Consent First
- No audio is recorded without explicit physical action
- All participants must validate their contributions
- Consent can be withdrawn until publication

### 1.2 Individual Sovereignty  
- Each speaker owns their words
- Personal consent rules are self-defined
- Right to deletion before consensus

### 1.3 Collective Benefit
- Validated data enters commons
- Contributors maintain access rights
- Revenue sharing from commercial use

## 2. Technical Architecture

### 2.1 Recording Phase

#### 2.1.1 Consent Signaling
```
CONSENT_SIGNAL = {
  type: "PHYSICAL_BUTTON_PRESS",
  indicator: "GREEN_LED_VISIBLE", 
  timestamp: ISO_8601,
  device_id: UUID,
  location: OPTIONAL_GPS
}
```

#### 2.1.2 Audio Capture
- Format: WAV/FLAC (lossless)
- Sample rate: 48kHz minimum
- Channels: Mono (directional mic)
- Local buffer: 30 seconds rolling
- Encryption: AES-256 at rest

### 2.2 Processing Phase

#### 2.2.1 Speaker Diarization
```
DIARIZATION_OUTPUT = {
  speakers_detected: INTEGER,
  segments: [{
    speaker_id: TEMP_UUID,
    start_time: FLOAT_SECONDS,
    end_time: FLOAT_SECONDS,
    confidence: FLOAT_0_TO_1
  }]
}
```

Minimum requirements:
- Speaker separation accuracy: >85%
- Minimum segment length: 2.0 seconds
- Overlap handling: Last speaker wins
- Maximum speakers: 10

#### 2.2.2 Consent Evaluation

Each participant's consent rules are evaluated:

```
CONSENT_RULES = {
  participant_id: UUID,
  prompt: STRING_MAX_500_CHARS,
  auto_approve_categories: ARRAY,
  auto_deny_categories: ARRAY,
  require_review: BOOLEAN
}

CONSENT_DECISION = {
  recording_id: UUID,
  participant_id: UUID,
  decision: ENUM["APPROVE", "DENY", "REVIEW"],
  reason: STRING,
  timestamp: ISO_8601
}
```

### 2.3 Validation Phase

#### 2.3.1 Participant Notification
- Method: Push notification or email
- Timeout: 72 hours default
- Content: Their segments only
- Interface: Web or app

#### 2.3.2 Validation Actions
```
VALIDATION_RESPONSE = {
  participant_id: UUID,
  recording_id: UUID,
  action: ENUM["APPROVE", "DENY", "EDIT"],
  edits: OPTIONAL[{
    segment_id: UUID,
    action: ENUM["REDACT", "DELETE"]
  }],
  timestamp: ISO_8601
}
```

### 2.4 Publication Phase

#### 2.4.1 Consensus Requirement
- ALL participants must approve
- One DENY = recording blocked
- Timeout = implicit DENY

#### 2.4.2 Published Data Format
```
PUBLISHED_RECORDING = {
  recording_id: UUID,
  date: ISO_8601,
  duration: SECONDS,
  participants: [{
    anonymous_id: UUID,
    segments: [SEGMENT_OBJECTS],
    consent_timestamp: ISO_8601
  }],
  transcript: OPTIONAL_STRING,
  metadata: {
    device_id: UUID,
    protocol_version: SEMVER,
    general_topic: OPTIONAL_STRING
  }
}
```

## 3. Privacy & Security

### 3.1 Data Minimization
- No names in transcripts
- No location beyond city level
- No identifiable metadata
- Automatic PII redaction

### 3.2 Encryption Standards
- Transit: TLS 1.3+
- Rest: AES-256-GCM
- Keys: Per-participant derived

### 3.3 Retention Policies
- Unvalidated: Delete after 7 days
- Denied: Immediate deletion
- Published: Indefinite (commons)

## 4. Governance

### 4.1 Protocol Changes
- Proposal: Any Genesis Partner
- Review: 7 day minimum
- Approval: 60% of Genesis Partners
- Veto: 40% can block

### 4.2 Dataset Governance
- License: CC-BY-SA initially
- Commercial use: Revenue share
- Model training: Open access
- Quality control: Community review

## 5. Implementation Requirements

### 5.1 Device Requirements
- Physical consent button
- Visual indicator (LED)
- Secure element for keys
- Local processing capability
- Network connectivity

### 5.2 Software Requirements
- Diarization engine
- Consent evaluation AI
- Validation interface
- Secure storage
- API endpoints

### 5.3 Infrastructure Requirements
- Distributed storage
- Processing pipeline
- Notification system
- Governance platform
- Analytics dashboard

## 6. Use Case Specifications

### 6.1 Podcast Recording
- Host pre-approves episodes
- Guests validate per segment
- Sponsors segments marked
- Music/ads excluded

### 6.2 Research Interviews
- IRB protocol integration
- Participant IDs not names
- Study-specific consent
- Academic license terms

### 6.3 Classroom Recording
- Parental consent for minors
- Teacher approval required
- Educational fair use
- Student anonymization

## 7. API Specification

### 7.1 Recording API
```
POST /recordings/start
POST /recordings/stop
GET /recordings/{id}/status
```

### 7.2 Validation API
```
GET /validations/pending
POST /validations/{id}/approve
POST /validations/{id}/deny
PUT /validations/{id}/edit
```

### 7.3 Dataset API
```
GET /dataset/search
GET /dataset/download
POST /dataset/contribute
```

## 8. Economic Model

### 8.1 Revenue Sources
- API access fees
- Commercial licenses
- Enterprise deployments
- Custom implementations

### 8.2 Revenue Distribution
- Genesis Partners: 5% pool
- Contributors: 30% pool
- Operations: 30%
- Development: 35%

## 9. Future Considerations

### 9.1 Version 2.0 Features
- Real-time transcription
- Multi-language support
- Emotional tone detection
- Video consent protocols

### 9.2 Scalability Goals
- 1M recordings/month
- 10K simultaneous devices
- 1PB dataset size
- <1 second validation

## 10. Reference Implementation

GitHub: [github.com/yes-pen/protocol](https://github.com/yes-pen/protocol)

Initial implementation priorities:
1. Basic consent mechanism
2. Simple diarization
3. Manual validation
4. Basic storage

## Appendices

### A. Glossary
- **Diarization**: Speaker separation in audio
- **Consensus**: All-party agreement
- **Genesis Partner**: Founding protocol governor
- **Validation**: Participant content review

### B. Prior Art
- Whisper (OpenAI): Transcription
- PyAnnote: Diarization
- Signal Protocol: Encryption
- Git: Distributed consensus

### C. Contact
- Protocol questions: protocol@yespen.org
- Genesis Partnership: genesis@yespen.org
- Security issues: security@yespen.org

---

*This is a living document. Propose changes via GitHub pull requests.*