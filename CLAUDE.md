# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Salesforce Consumer Goods Cloud (CGC) customization package** — a metadata-driven mobile field-sales application for retail management. It depends on SAP Consumer Goods Cloud Managed Package v260.0. There is no traditional build system; deployment is done through the Salesforce metadata API or SAP CGC deployment tooling using `package.xml`.

## Architecture

The codebase has ~7,100 files split evenly between JavaScript (`.bl.js` — business logic) and XML (configuration/metadata). The project is organized into ~49 functional modules:

**Core domains:**
- `Order/` — Order lifecycle, pricing calculations, BO/LO order models
- `Application/` — Main dashboard, cockpits, agenda, user-facing navigation
- `BusinessPartner/` — Customer/supplier data and relationships
- `Product/` — Product catalog management
- `Visit/` — Field visit planning and tracking
- `Inventory/` — Stock management and replenishment
- `Promotion/` — Promotion rules and approval workflows
- `Payment/` — Payment processing and templates
- `Contract/` — Contract management

**Shared infrastructure:**
- `Plugins/` — Reusable logic plugins (e.g., `BLConstants`, `ComplexPricingEngine`)
- `UI Plugins/` — Reusable UI components
- `Utilities/` — Shared helper functions
- `Locale/` — Localization strings

### Module internal structure

Each functional module typically follows this layout:

```
ModuleName/
├── BO/    # Business Objects — domain models with validation and lifecycle hooks
├── DS/    # Data Sources — query/fetch definitions
├── LO/    # List Objects — backing models for list screens
├── PL/    # Process Logic — workflow and process steps
├── PR/    # Presentation — UI screen definitions
└── TB/    # Tables / static resources
```

### File naming conventions

- `[ObjectName].[MethodName].bl.js` — JavaScript business logic files
- `*.businessobject.xml` — BO definitions
- `*.listobject.xml` / `*.listitem.xml` — List model definitions
- `*.userinterface.xml` — UI screen definitions
- `*.processflow.xml` — Workflow/process definitions
- `*.datasource.xml` — Data source configuration
- `*.validationmessages.xml` — Validation error messages

### Business logic patterns

JavaScript files hook into the SAP CGC object lifecycle:

- `BeforeSaveAsync` / `AfterSaveAsync` — pre/post-save hooks
- `AfterLoadAsync` — post-load initialization
- `Validate` / `DoValidateAsync` — validation logic
- `Initialize` / `CreateAsync` — object construction
- `SetEARights` — field-level access control

Validation functions receive a `messageCollector` parameter; errors are pushed as `{ level, objectClass, messageID }` objects. The corresponding message IDs are defined in the matching `.validationmessages.xml` file.

All `.bl.js` files use strict mode and JSDoc annotations (`@function`, `@this`, `@namespace`, `@param`). Auto-generated scaffold code contains marked insertion points for customization — edit only within those sections.

### Key configuration files

- `package.xml` — declares the managed-package dependency (SAP CGC v260.0); required for deployment
- `manifest.json` — file fingerprints used by the deployment tooling for integrity checking; regenerated on deployment, do not hand-edit
