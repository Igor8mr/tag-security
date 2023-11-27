# CloudEvents Self-assessment

September 26, 2023

Authors: Igor Rodrigues (@igor8mr), Matthew Gong (@MatthewZGong), Kushal Kothari
(@Kushal-kothari) and Devyani Bairagya (@devyani-14)

Contributors/Reviewers: Pranava Kumar Vemula (@Rana-KV)

This document elaborates and explores the design goals for CloudEvents as well
as a security assessment of the software.

## Table of contents

* [Metadata](#metadata)
  * [Security links](#security-links)
* [Overview](#overview)
  * [Background](#background)
  * [Actors](#actors)
  * [Actions](#actions)
  * [Goals](#goals)
  * [Non-goals](#non-goals)
* [Self-assessment use](#self-assessment-use)
* [Security functions and features](#security-functions-and-features)
* [Project compliance](#project-compliance)
* [Secure development practices](#secure-development-practices)
  * [Development Pipeline](#development-pipeline)
  * [Communications Channels](#communications-channels)
  * [Ecosystem](#ecosystem)
* [Security issue resolution](#security-issue-resolution)
* [Appendix](#appendix)
  * [Known Issues Over Time](#known-issues-over-time)
  * [CII Best Practices](#cii-best-practices)
  * [Case Studies](#case-studies)
  * [Related Projects / Vendors](#related-projects-vendors)

## Metadata

|   |  |
| -- | -- |
| Software | [CloudEvents Repository](https://github.com/cloudevents/spec)  |
| Security Provider | No  |
| Languages | ANTRL, Python, C#/.NET, Go, Java, Javascript, PHP, PowerShell, Ruby and Rust |
| SBOM | Software bill of materials.  Link to the libraries, packages, versions used by the project, may also include direct dependencies. |
| | |

### Security links

| Doc | url |
| -- | -- |
| CloudEvents Security Assessment  | https://github.com/cloudevents/spec/blob/main/docs/CE-SecurityAudit-2022-10.pdf |

## Overview

CloudEvents is an open-source specification for describing data events. Such
events are frequently used across many industry fields, but there are currently
no standards on how to write those. Because of that, which is currently very
different across different developers. This makes programmers have to develop
new event handling logic for each event source and makes it hard for two systems
to communicate with each other. CloudEvents seeks to dramatically simplify event
declaration and delivery across services, platforms, and beyond.

![CloudEvents Logo](images/cloudevents-logo.png)

The project also provides software development kits (SDKs) for Go, JavaScript,
Java, C#, Ruby, PHP, PowerShell, Rust, and Python. These can be used to build
event routers, tracing systems, and other tools.

### Background

#### Event

An "event" is a data record expressing an occurrence and its context. Events are
routed from an event producer (the source) to interested event consumers. The
routing can be performed based on information contained in the event, but an
event will not identify a specific routing destination. Events will contain two
types of information: the Event Data representing the Occurrence and Context
metadata providing contextual information about the Occurrence. A single
occurrence MAY result in more than one event.

There is no common language on events themselves. So when a new software is
created developers have to write new event handling processes for different
types of sources. This can become very chaotic and unorganized.

CloudEvents was developed to address the lack of uniformity in event data
formats that exist in cloud and microservices environments. By providing a
standardized way to represent events, CloudEvents aims to enhance
interoperability, portability, and simplicity in event-driven architectures
across different cloud providers and services.

### Actors

#### Event Consumer

 The entity/system interested in subscribing to different events. Receiving
 Events from the producer will trigger further action that is up to the
 consumer. A "consumer" receives the event and acts upon it, which might lead to
 the occurrence of new events.

#### Event Producer

 The entity/system that produces the "events". They are responsible for wrapping
 event data in the CloudEvents specification.


#### Event Mediator/Intermediary

 Depending on the architecture of the system, the event mediator is the
 entity/system that is responsible for the distribution, processing and routing
 of events to consumers. The event broker ensures reliable delivery and may
 enforce security policies. 

### Actions

#### Event Formating

Called by the producer and the consumer. An Event Format specifies how to we
want to serialize a CloudEvent as a sequence of bytes. Stand-alone event
formats, such as the JSON format, specify serialization independent of any
protocol or storage medium. The producer encodes the event, while the consumer
decodes the event.

#### Protocol Binding

A protocol binding describes how events are sent and received over a given
protocol.

Protocol bindings MAY choose to use an Event Format to map an event directly to
the transport envelope body, or MAY provide additional formatting and structure
to the envelope. For example, a wrapper around a structured-mode message might
be used, or several messages could be batched together into a transport envelope
body.

### Goals

* Cloud Events aims to simplify event declaration and delivery across services,
  platforms and systems.
* Cloud Events aims to create interoperability and portability between different
  systems and services in event driven systems.

### Non-goals

* Cloud Events does not aim to change the implementation details of underlying
  communication protocols. 
* Cloud Events does not want to define the processing logic for events within
  different application and systems.

## Self-assessment use

This self-assessment is created by the CloudEvents team to perform an internal
analysis of the project's security. It is not intended to provide a security
audit of CloudEvents, or function as an independent assessment or attestation of
CloudEvents's security health.

This document serves to provide CloudEvents users with an initial understanding
of CloudEvents's security, where to find existing security documentation,
CloudEvents plans for security, and general overview of CloudEvents security
practices, both for development of CloudEvents as well as security of
CloudEvents.

This document provides the CNCF TAG-Security with an initial understanding of
CloudEvents to assist in a joint-assessment, necessary for projects under
incubation.  Taken together, this document and the joint-assessment serve as a
cornerstone for if and when CloudEvents seeks graduation and is preparing for a
security audit.

## Security functions and features

### Critical Security Components

* Event Identification: Every event within CloudEvents is uniquely identified by
  a specific combination of `source` and `id`. Producers must guarantee that
  each unique event's concatenation of `source` and id` remains distinctive.
  This practice aids in distinguishing events and preventing the processing of
  duplicate events.

* Event Type: The `type` attribute holds a value that characterizes the nature
  of the event associated with the initial incident. This attribute is
  frequently utilized for routing, observability, policy enforcement, and
  similar purposes. The producer determines the format, which may contain
  details such as the version of the `type`.

* Event Subject: The `subject` attribute explains the event's subject within the
  context of the event producer. Clarifying the subject in contextual metadata
  proves particularly beneficial in scenarios involving generic subscription
  filtering, where middleware may lack the ability to interpret the content
  within the `data` attribute.

* Event Data Integrity: Encryption should be applied to domain-specific event
  data to limit visibility to trusted entities. The specific encryption
  mechanism used is a mutual agreement between producers and consumers.

* Privacy and Sensitive Information Handling: Context attributes should not
  carry or represent sensitive information. CloudEvent producers, consumers, and
  intermediaries can inspect and log context attributes.
  
### Security Relevant Components

* Transport Security: Although CloudEvents does not prescribe specific transport
  security mechanisms, it is typically conveyed over secure protocols such as
  HTTPS, ensuring integrity and confidentiality.

* Event Source Authentication: The `source` attribute within a CloudEvent
  provides context for the event occurrence, establishing reliable and secure
  source identification.
  
* Data Schema Verification: The `data` attribute in a CloudEvent contains the
  actual event data, and its schema can be defined and validated for consistency
  and accuracy. This verification process helps to prevent issues arising from
  malformed or unexpected data.

## Project compliance

**`TO-DO`**

* Compliance.  List any security standards or sub-sections the project is
  already documented as meeting (PCI-DSS, COBIT, ISO, GDPR, etc.).

## Secure development practices

### Development Pipeline 

* You can either report a change or create a Github Issue
  * Check existing issues first
* All Proposed changes will be done through Github Pull Requests (PRs)
* All patches must be signed by the commiter
  * The sign-off is a simple line at the end of the explanation for the patch
* Pull Requests will be review during official meetings 
  * off-line reviews are recommended before meetings 
  * Meetings happen every Thursday at 9AM PT 
* Pull Requests will be resolved and merged during official meetings as a result
  of a motion
  * Pull Requests should not be merged if substantial changes have been made in
    the past 2 days

### Communications Channels

* Internal
  * Meetings
  * Email
  * Github Issues

* InBound
  * [CloudEvents CNCF Email Address](cncf-cloudevents@lists.cncf.io)
  * [CNCF Slack workspace](http://slack.cncf.io/)
  * [CNCF Meeting Calender](https://www.cncf.io/community/calendar/)
* Outgoing
  * [CloudEvents CNCF
    Subscription](https://lists.cncf.io/g/cncf-cloudevents-sdk)

### Ecosystem

CloudEvents is integrated with various different cloud-native technologies and
services. It goal is to create interoperability in event-driven architecture

CloudEvents has many adopters including but not limited to:

* Adobe I/O Events
* Azure Event Grid
* Google Cloud Eventarc
* VMware Event Broker Appliance

## Security issue resolution

### Responsible Disclosures Process

CloudEvents provides a dedicated [email
address](cncf-cloudevents-security@lists.cncf.io) for reporting security
concerns related to the specification or the SDKs.

* Responsible Disclosures Process. A outline of the project's responsible
  disclosures process should suspected security issues, incidents, or
vulnerabilities be discovered both external and internal to the project. The
outline should discuss communication methods/strategies.
  * Vulnerability Response Process. Who is responsible for responding to a
    report. What is the reporting process? How would you respond?

### Incident Response

A description of the defined procedures for triage, confirmation, notification
  of vulnerability or security incident, and patching/update availability.

## Appendix

### Known Issues Over Time

#### Issues found by Trail of Bits

The main security assessment on CloudEvents was a [Security Audit performed by
Trail of
Bits](https://github.com/cloudevents/spec/blob/main/docs/CE-SecurityAudit-2022-10.pdf)
released on October 26, 2022. These were mainly concerning the different
CloudEvents SDK, not the specification.

##### [Java SDK] Reliance on default encoding

* Severity: Undetermined
* Difficulty: Low
* Type: Undefined Behavior
* Finding ID: TOB-CE-1
* Target: Java SDK

Several instances were found where the getByte() standard Java API is utilized
without specifying encoding, leading the Java SDK to rely on system default
encoding. This can result in varying processing of event data across platforms.
While the specification mandates adherence to appropriate and RFC-compliant
encodings, there is room for improvement in the Java SDK implementation and
documentation to emphasize the significance of consistent encoding among actors.
Although not all instances are problematic, especially when handling binary
data, it is crucial to document and address this behavior in the SDK
implementation, documentation, and provided examples.

##### [Java SDK] Outdated Vulnerable Dependencies

* Severity: Undetermined
* Difficulty: Medium
* Type: Patching
* Finding ID: TOB-CE-2
* Target: Java SDK

The Java SDK contains multiple outdated dependencies with publicly known
vulnerabilities, including high- and medium-risk ones. The snyk tool
automatically audited each module due to time constraints and ease of
remediation. Manual review of exploitability within the SDK's context was not
conducted.

##### [JavaScript SDK] Potential XSS in httpTransport()

* Severity: Undetermined
* Difficulty: Low
* Type: Data Validation
* Finding ID: TOB-CE-3
* Target: sdk-javascript/src/transport/http/index.ts

The JavaScript SDK's httpTransport() method exposes raw error messages from the
endpoint, potentially leading to XSS vulnerabilities if user-controlled data is
reflected without proper sanitization in the rendered web page. While the
specification doesn't mandate validation or sanitization, the SDK documentation
should emphasize the risk of unsanitized HTTP responses when using this API in
an emitter.

##### [Go SDK] Outdated Vulnerable Dependencies

* Severity: Undetermined
* Difficulty: Low
* Type: Patching
* Finding ID: TOB-CE-4
* Target: Go SDK

The Go SDK has multiple outdated dependencies with known vulnerabilities. The
open-source snyk tool automatically audited each module. Due to time constraints
and ease of remediation, manual review of exploitability within the SDK's
context was skipped.

##### [Go SDK] Downcasting of 64-bit integer

* Severity: Undetermined
* Difficulty: Low
* Type: Undefined Behavior
* Finding ID: TOB-CE-5
* Target: sql/v2/parser/expression_visitor.go, sql/v2/utils/casting.go

The strconv.Atoi function parses a machine-dependent integer (int64 for 64-bit
targets). In some code instances, the result from strconv.Atoi is later
converted to a smaller type (int16 or int32), risking overflow with specific
inputs.

##### [Go SDK] ReadHeaderTimeout not configured

* Severity: Informational
* Difficulty: Low
* Type: Denial of Service
* Finding ID: TOB-CE-6
* Target: Go SDK

The Go http.server API offers four timeouts, including ReadHeaderTimeout.
Failure to set a value for this timeout makes the listener instance susceptible
to Slowloris DoS attacks.

##### [CSharp SDK] Outdated Vulnerable Dependencies

* Severity: Undetermined
* Difficulty: Low
* Type: Patching
* Finding ID: TOB-CE-7
* Target: CSharp SDK

The CSharp SDK has multiple outdated dependencies with known vulnerabilities.
Using the open-source snyk tool, each module was automatically audited. Due to
time constraints and ease of remediation, manual review of exploitability within
the SDK's context was skipped.

### CII Best Practices

**`TO-DO`**

* [CII Best
  Practices](https://www.coreinfrastructure.org/programs/best-practices-program/).
  Best Practices. A brief discussion of where the project is at with respect to
  CII best practices and what it would need to achieve the badge.

### Case Studies

**`TO-DO`**

* Case Studies. Provide context for reviewers by detailing 2-3 scenarios of
  real-world use cases.

### Related Projects / Vendors

#### OpenTelemetry

[OpenTelemetry Website](https://opentelemetry.io/)

OpenTelemetry is a collection of APIs, SDKs, and tools. Use it to instrument,
generate, collect, and export telemetry data (metrics, logs, and traces) to help
you analyze your software’s performance and behavior.

#### AsyncAPI

[AsyncAPI Website](https://www.asyncapi.com/)

AsyncAPI is an open source initiative that seeks to improve the current state of
Event-Driven Architectures (EDA). Our long-term goal is to make working with
EDAs as easy as working with REST APIs. That goes from documentation to code
generation, and from discovery to event management.

#### Event-B

[An Introduction to the Event-B Modelling
Method](https://www.southampton.ac.uk/~tsh2n14/publications/chapters/eventb-dbook13.pdf)

Event-B is a formal method for system-level modelling and analysis. Key features
of Event-B are the use of set theory as a modelling notation, the use of
refinement to represent systems at different abstraction levels and the use of
mathematical proof to verify consistency between refinement levels.

#### Apex Event Specification

[Apex Event Specification
Guide](https://insights.eventscouncil.org/Portals/0/APEX_Event_Specifications_Guide.pdf)

The APEX Event Specifications Guide (ESG) is a written document that contains
all the details of an event. The ESG is used by event organizers to communicate
information to venues and suppliers. The ESG is a three-part template that
includes: Narrative, Schedule, Function orders.

This is an older document used across many engineering fields, which is not
restricted to computer science, making it different from the other examples.
However, it is an example of event specification being widely used in industry,
including more physical areas such as mechanical engineering and factories.