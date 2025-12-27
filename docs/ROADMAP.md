# n8n-nodes-jobber Roadmap

**Package:** `@arisegroup/n8n-nodes-jobber`
**Version:** 0.1.1
**Status:** Production Ready

---

## Current State

The Jobber n8n node is **feature-complete** for current requirements. All core functionality is implemented and tested.

### Implemented Features

| Category | Feature | Status |
|----------|---------|--------|
| **Authentication** | OAuth2 flow | Complete |
| **Clients** | Full CRUD | Complete |
| **Jobs** | Full CRUD | Complete |
| **Quotes** | Full CRUD + convertToJob | Complete |
| **Invoices** | Full CRUD | Complete |
| **GraphQL** | Raw query execution | Complete |
| **Webhooks** | 17 event triggers | Complete |
| **Security** | HMAC-SHA256 verification | Complete |
| **Pagination** | Cursor-based | Complete |
| **UI** | Dynamic dropdown loading | Complete |

---

## No Active Enhancement Roadmap

The Jobber node is intentionally kept stable and minimal. The **GraphQL escape hatch** allows users to execute any query/mutation not covered by standard operations.

### Why No Roadmap?

1. **Feature Complete** - All common operations are covered
2. **GraphQL Flexibility** - Custom queries handle edge cases
3. **Stability Priority** - Production workloads depend on this node
4. **API Alignment** - Node mirrors Jobber's API capabilities

---

## Potential Future Enhancements

If community demand warrants, these could be considered:

| Enhancement | Priority | Complexity | Notes |
|-------------|----------|------------|-------|
| Assessments CRUD | Low | Medium | Not commonly used |
| Properties CRUD | Low | Medium | Available via GraphQL |
| Timesheets operations | Low | Medium | Available via GraphQL |
| Batch operations | Medium | High | Rate limit considerations |
| Additional webhook filtering | Low | Low | Current filtering sufficient |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.1.1 | Dec 2024 | Fixed package.json repo URL |
| 0.1.0 | Dec 2024 | Initial release with full functionality |

---

## Contributing

The node is open source. For contributions:
1. Fork the repository
2. Create feature branch
3. Submit PR with tests
4. Ensure ESLint passes

---

## Related Projects

- **n8n-nodes-quo** - Companion node for Quo (OpenPhone) integration
- **Plotter Mechanix** - Primary use case driving development

---

*Last Updated: December 26, 2025*
