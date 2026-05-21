# Waka — Technical Position

Waka is a protocol-oriented application for creating sovereign data trusts and producing portable, witnessed claims.

It has two operating paths.

**Sovereign anchoring** creates a durable digital record for a project, place, community, or stewardship body. It uses project identity, resource storage, content addressing, and external anchors to give a project custody over its own records.

**The claims engine** uses the full Waka architecture. Resources become claim subjects. Validation nodes apply human or machine witness methods to those subjects. Claims are composed from validated resources, and anchors make those claims portable into downstream systems.

This document is the technical companion to the [Conceptual Position](./CONCEPTUAL_POSITION.md). The conceptual position explains what Waka is and who it is for. This document explains how Waka is structured: its object model, protocol layers, open standards, claims engine, anchoring substrates, and implementation architecture.

---

## Architecture overview

Waka is organized around five core objects:

```txt
projects → resources → validation nodes → claims → anchors
