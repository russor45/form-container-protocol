#  Form Container Protocol

A framework-agnostic, schema-driven protocol for orchestrating accessible, agentic form containers across modal, inline, and side-panel contexts. Designed to unify interaction boundaries, reduce cognitive overhead, and enable LLM-friendly automation.

---

## 🚀 Purpose

This protocol formalizes the behavioral contract for form containers—how they mount, validate, submit, and communicate state—without prescribing implementation details. It empowers design systems to remain flexible while ensuring consistency, accessibility, and composability.

---

## 📐 Core Principles

- Protocol over pattern: Abstracts common form behaviors into a reproducible contract.
- Accessibility-first: Aligns with ARIA roles and WCAG standards.
- Slot-aware: Supports declarative slot contracts for header, body, footer, and affordances.
- Agentic orchestration: Enables automation and LLM integration via predictable state flows.
- Framework-neutral: Reference implementations provided in ShadCN Vue, Material React, and more.

---

## 📁 Repository Structure

`text
/spec/                    # Canonical spec and appendices
/src/                     # ShadCN Vue, Material UI, etc.
/docs/                    # Protocol at a glance, FAQ, changelog
/tests/                   # Unit, integration, accessibility tests
`

---

## 📜 Specification

The full protocol is defined in spec/protocol.md, covering:

1. Mounting & lifecycle
2. Validation boundaries
3. Submission semantics
4. Accessibility mapping
5. Slot contracts
6. State orchestration
7. Error handling
8. Focus management
9. Form affordances
10. External control
11. Compliance checklist

See appendices for migration guides, mental models, and implementation examples.

---

## 🧪 Reference Implementations

- ShadCN Vue
- Material React
- Ant Design

Each includes usage examples, slot mapping, and test coverage.

---

## 🧠 Mental Model

Form containers are treated as orchestrators—not just wrappers. They expose a predictable interface for agents and humans alike, enabling automation, accessibility, and composability without sacrificing flexibility.

See [Appendix F](spec/appendices/F-mental-model.md) for a deep dive.

---

## ✅ Compliance

Use the tools/compliance-check.js to validate your implementation against the protocol. Or follow the manual checklist in Appendix I.

---

## 🤝 Contributing

We welcome feedback, critique, and reference implementations. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## 🗓️ [History](docs/HISTORY.md)

Evolution, milestones, and version rationale.

- **August 2025** — Initial public release (v0.1.0 draft).
- **Planned** — v1.0 stable release after community review.

---

## 📄 License & Attribution

This project is licensed under the **Apache 2.0 License** — you are free to use, modify, and distribute, but **must provide attribution** to the original author.
When referencing this protocol, please cite:

> Form Container Protocol — Created by Richard, 2025


---

## 🧭 Learn More

- [Protocol at a Glance](docs/protocol-at-a-glance.md)
- [FAQ](docs/FAQ.md)
- [Changelog](docs/CHANGELOG.md)
