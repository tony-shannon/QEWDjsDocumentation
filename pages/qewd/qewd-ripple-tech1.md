---
title:  Explaining Ripple-QEWD
keywords: QEWD
sidebar: rippledocs1_sidebar
permalink: qewd-ripple-explained.html
---


**Ripple’s QEWD.js-based Middle Tier**

 

The Ripple Showcase stack combines three key Open Source frameworks to offer a quick and easy, yet enterprise-ready, stack to support a wide variety of application needs in the health and care arena. In simple terms the three key components are tiered, and can be understood in terms of front-end (UI), middleware and back-end (DB) elements respectively:

**PulseTile** - UX/UI framework- for great usability at the clinical frontline

**QEWDjs**- Integration Framework- Quick & Easy/Quality Enterprise Integration

**EtherCIS**- Clinical Data Repository - for Structured & Unstructured Data


----------


The PulseTile UX/UI framework acts as the clinical front-end to the showcase stack and has been crafted as a standalone framework, needing RESTful APIs for data retrieval/input     

 
The Ripple Foundation has chosen QEWD.js, a leading-edge Open Source NodeJS based framework, to power the middle tier of its showcase stack.

This middleware layer is known as **Ripple-Qewd** or (**_qewd-ripple_**).  It is a multi-functional and high-performance solution, powered by the QEWD.js framework, which fulfils a number of roles:


 **Roles of QEWD-Ripple in the Ripple Showcase Stack**

**1) Front End Facing RESTful API service**

- QEWD-Ripple acts as a middleware service (including web-server) to support the RESTful API endpoints  required by the PulseTile User Interface framework.  

- QEWD.js uses Express.js to provide the web server functionality (Koa.js is also a configurable alternative)

-     The middle tier implements the RESTful endpoint server-side logic

**2) Security Broker**

-       It implements and manages the security for the Ripple showcase stack, including both the frontend PulseTile application and (in progress) external/backend systems such as EtherCIS, 

-  The security approach involved in the showcase "secure mode" is currently based on OAuth2/openID Connect using JSON Web Tokens (JWTs). For the purposes of the demonstrator we are using a cloud based provider for authentication (Auth0.com), although, of course, this could be swapped out

- In addition, the Qewd-Ripple middleware handles Role Based Access Control features.  Currently these are set as either Professional (IDCR access) or Patient (PHR) access, but, of course, this can be modified in any way necessary

- The QEWD.js middleware provides several levels of audit trail, one via the QEWD-monitor application, and also, optionally, using its "resilient mode".   These facilities mean that any/all access can be examined at a session- or individual message-based level

**3) Integration & Federation Framework**

-       Qewd-Ripple also acts as an integration and  federation platform, communicating with any/all internal data sources required plus any/all legacy/external systems that need to be connected to/integrated with. 

- Some example integrations include the sample Patient Administration Service (RippleMiniPAS), Clinical Data Repositories (including openEHR based systems Marand and EtherCIS) and a Vendor Neutral Archive/Dicom Imaging Server (known as Orthanc).

Much of the work of the middle tier involves it acting as a go-between, satisfying the demands of the PulseTile User Interface, in terms that it understands, and the remote external systems (eg EtherCIS openEHR CDR), in terms that they understand.  This mainly involves transforming JSON objects between the format sent from and expected by the PulseTile UI and the format required by and sent from the external systems.  
In particular each "clinical heading" requires a specific set of transformations between the UI (PulseTile) format and the DB (EtherCIS/OpenEHR) format.

Communication with the external systems involves:

 -       Fetching, for a selected patient, data relating to one or more clinical headings (eg allergies, medications, problems, etc).  This requires the middle tier to send REST requests to the connected systems.  These requests consist of the rest endpoint as well as the query syntax. As several of the Ripple Showcase stack endpoints are based on the openEHR standard, AQL is the query syntax/language involved, though SQL and GraphQL can be used as alternatives. 

-       Saving or updating data for a clinical heading for a selected patient. This requires the Qewd-Ripple middle tier to send REST requests in line with an agreed JSON API format. In the example of the EtherCIS Clinical Data Repository the current format used in known as  the OpenEHR "flat JSON" format.

- Work is underway towards alignment with the FHIR API standards.. In essence QEWD-Ripple can converse with any database, any message standard (XML/JSON etc) so is very flexible in that regard.

To simplify and automate this transformation work, the middle tier includes a JSON transformation engine ([https://github.com/robtweed/qewd-transform-json)](https://github.com/robtweed/qewd-transform-json)) which allows the transformations to be described via a template which is, itself, a JSON document.  The template syntax allows not only mapping from locations within the source (input) JSON document, but also the use of custom functions for more complex transformations (eg date formatting) to transform as the external (output) system requires.  An associated graphical editor tool ([https://github.com/robtweed/qewd-transform-json-editor)](https://github.com/robtweed/qewd-transform-json-editor)) can be used to facilitate the design and maintenance of the transformation template document files, which greatly simplifying the transformation process.

**4) High Performance Cache**

-     Qewd-Ripple also provides an integrated high-performance session cache, which minimises the traffic to and from external systems.  This makes use of the persistent JSON objects and document database within QEWD.js which, in turn, is supported by either the Redis or GT.M open source databases acting as the cache.


**5) Catering for the Unique Database Technology Mix in Healthcare**

 

In designing the Ripple Middle Tier, it has been critically important that it is capable of:

* supporting mainstream databases (eg MySQL etc) via the npm ecosystem

* federating external data repositories such as Ethercis via standard protocols such as RESTful APIs

These, of course, are standard requirements for most middle tier platforms.  However, there’s a further capability that QEWD.js offers that turns out to be crucially important in healthcare.

One of the specific design features of QEWD.js is its ability to seamlessly integrate with what are often referred to as "Global Storage" databases. This database technology has a long history in healthcare, but is little known and poorly understood in the IT mainstream.  They are described in this paper on Global Storage technologies: [http://www.mgateway.com/docs/universalNoSQL.pdf](http://www.mgateway.com/docs/universalNoSQL.pdf)

This paper describes how such databases can behave, simultaneously, as: 

* Key Value Storage

* Tabular( and Columnar) Storage

* Document (inc JSON/XML) Storage

* Graph DB

* Object Storage (e.g. Images)

* Relational DB (SQL)




There are two leading databases in this category: Intersystem Cache (proprietary) and FIS GT.M (open source) - all with In-memory Performance and On-disk Integrity.  They are commonly found in the financial sector, but, crucially significantly for Ripple, they dominate the healthcare IT market-place found in solutions from major healthcare IT suppliers such as EMIS, Epic, Intersystems, VA (Veterans).  These technologies are made natively accessible to the Node.js ecosystem via QEWD.js (the only Node.js framework designed to do so).  This, then, is a key reason for its choice as the framework for Ripple’s middle tier.

Furthermore, the Ripple Foundation has supported QEWD.js development work to create an abstraction on top of the highly-popular, well-understood Redis database, so that it, too, can be leveraged as a multi-model "Global Storage" database using identical APIs to those used for Cache and GT.M.

As a result, by using QEWD.js as the basis of its middle tier, Ripple is uniquely positioned to easily integrate with the most important and most common database technologies that are found in typical healthcare enterprises.

