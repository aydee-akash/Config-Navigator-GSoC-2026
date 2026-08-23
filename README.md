![Google Summer of Code Logo](https://developers.google.com/open-source/gsoc/resources/downloads/GSoC-logo-horizontal.svg)

# Google Summer of Code 2026 - Final Report

**Project:** AI-Powered Configuration Navigator

---

**Contributor:** Akash Deep
**Organization:** Drupal
**Mentor:** Stephen Mustgrave (smustgrave)
**Project Link:** [Config Navigator on Drupal.org](https://www.drupal.org/project/config_navigator)

---

## 1. Project Overview & Goals

Config Navigator is a tool for Drupal that lets you find and understand admin settings by asking questions in plain language. Instead of digging through menus and documentation, you ask what setting you need and it takes you there.

Drupal has a huge number of configuration settings, and finding the right one is difficult if you don't already know where it lives. The information already exists in Drupal, it just isn't easy to navigate. An admin shouldn't need to know Drupal's menu structure or terminology to answer something like "where do I change the site logo?". Natural language search makes that much more intuitive.

The objectives set out in the proposal were:

- A chat assistant with a persistent help icon on admin pages
- Natural language understanding of configuration questions
- Instant navigation to the right page
- Follow-up questions and conversation context
- Suggestions based on the current page and the user's role
- Fuzzy matching, synonyms and Drupal-specific terminology
- Visual highlights on the destination page
- A learning system that improves answers from user feedback
- A configuration index storing page purposes and keywords
- Integration with an AI provider
- Conversation history storage
- A feedback mechanism
- RESTful endpoints
- A plugin API so other modules can register their own configuration pages

---

## 2. Work Accomplished

### The assistant and its interface

- Chat widget with a persistent help icon on admin pages ([#3606897](https://www.drupal.org/project/config_navigator/issues/3606897))
- Natural language matching through the Drupal AI module ([#3606895](https://www.drupal.org/project/config_navigator/issues/3606895))
- Visual highlights on the page after navigating from the assistant ([#3615630](https://www.drupal.org/project/config_navigator/issues/3615630))
- Suggestions based on the admin page the person is currently on ([#3615629](https://www.drupal.org/project/config_navigator/issues/3615629))
- Related configuration pages returned alongside the answer ([#3616356](https://www.drupal.org/project/config_navigator/issues/3616356))
- Warnings on pages where a change has wide effects ([#3616361](https://www.drupal.org/project/config_navigator/issues/3616361))

### Search and matching

- The configuration index service ([#3601652](https://www.drupal.org/project/config_navigator/issues/3601652))
- Keyword search over the index ([#3606894](https://www.drupal.org/project/config_navigator/issues/3606894))
- Fuzzy matching, synonyms and tokenized questions ([#3615633](https://www.drupal.org/project/config_navigator/issues/3615633))
- Purposes and keywords stored against indexed pages ([#3616359](https://www.drupal.org/project/config_navigator/issues/3616359))
- Module help text used to describe pages ([#3616363](https://www.drupal.org/project/config_navigator/issues/3616363))
- Index widened beyond /admin/config ([#3616780](https://www.drupal.org/project/config_navigator/issues/3616780))
- A training dataset of common configuration queries ([#3616355](https://www.drupal.org/project/config_navigator/issues/3616355))

### Integration and API

- RESTful endpoints for chat, search and navigation ([#3615632](https://www.drupal.org/project/config_navigator/issues/3615632))
- Plugin API for contrib modules to register configuration pages ([#3606900](https://www.drupal.org/project/config_navigator/issues/3606900))
- Automatic index rebuilding ([#3616343](https://www.drupal.org/project/config_navigator/issues/3616343))
- Caching for AI matching results ([#3615315](https://www.drupal.org/project/config_navigator/issues/3615315))

### Privacy, permissions and feedback

- Permission-aware filtering of results ([#3606896](https://www.drupal.org/project/config_navigator/issues/3606896))
- A dedicated permission for using the assistant ([#3616345](https://www.drupal.org/project/config_navigator/issues/3616345))
- Only matching pages sent to the AI provider, with an option to turn AI off entirely ([#3616362](https://www.drupal.org/project/config_navigator/issues/3616362))
- Feedback mechanism on answers ([#3606899](https://www.drupal.org/project/config_navigator/issues/3606899))
- Stored feedback used to improve later answers ([#3616360](https://www.drupal.org/project/config_navigator/issues/3616360))
- Conversation history that survives a session ([#3615631](https://www.drupal.org/project/config_navigator/issues/3615631))
- Conversation history and follow-up support ([#3606898](https://www.drupal.org/project/config_navigator/issues/3606898))

### Quality

- Output escaping and error handling in the chat widget ([#3615309](https://www.drupal.org/project/config_navigator/issues/3615309))
- Test coverage for the configuration index service ([#3613351](https://www.drupal.org/project/config_navigator/issues/3613351))
- Test coverage for AI-powered matching ([#3613975](https://www.drupal.org/project/config_navigator/issues/3613975))
- Documentation ([#3606901](https://www.drupal.org/project/config_navigator/issues/3606901))

---

## 3. The Current State

The module is functional and installable from the 1.0.x branch. All fourteen deliverables from the proposal are built. Thirty issues were opened over the course of the project, twenty of them now fixed.

Every merge request runs the full Drupal GitLab CI pipeline, covering coding standards, spell checking, and kernel, functional and functional JavaScript tests. There is no tagged release yet.

Search quality was measured against a fixture of twenty questions phrased the way a site builder would actually ask them, each paired with the page that answers it:

| Stage | Questions resolved |
| --- | --- |
| Whole-phrase matching only | 0 of 20 |
| With tokenized search and synonyms | 9 of 20 |
| With the index widened beyond /admin/config | 16 of 20 |
| With page keywords and purposes as well | 20 of 20 |

The fixture is committed alongside a test that runs it, so the number can be watched over time rather than guessed at.

---

## 4. What's Left to Do

**Merge the remaining work.** A number of merge requests are still in review. Landing those is the first priority, since everything else depends on the module being in one piece.

**User testing.** This is the part I would most want to do next. I would want to see how people with different levels of Drupal experience actually phrase their questions, where the search gives useful results and where it gives misleading ones, and use that to improve matching and ranking. The training dataset gives a place to record what comes out of it, since a question someone asked and the assistant answered badly can go straight into the fixture.

**A tagged release.** There is no release yet. Once the remaining work is merged and anything surfaced by testing is cleaned up, a first release would make the module available for wider use.

---

## 5. Code Contributions

- **Project:** https://www.drupal.org/project/config_navigator
- **Commit history:** https://git.drupalcode.org/project/config_navigator/-/commits/1.0.x
- **Issue queue:** https://www.drupal.org/project/issues/config_navigator
- **Merge requests:** https://git.drupalcode.org/project/config_navigator/-/merge_requests

Thirty issues and twenty-five merge requests over the course of the project.

**A few worth looking at:**

- [#3606900: Add plugin API for contrib module config registration](https://www.drupal.org/project/config_navigator/issues/3606900)
- [#3615632: Add RESTful endpoints for chat, search, and navigation](https://www.drupal.org/project/config_navigator/issues/3615632)
- [#3616362: Avoid sending unnecessary site data to external AI providers](https://www.drupal.org/project/config_navigator/issues/3616362)
- [#3616780: Index configuration pages outside /admin/config](https://www.drupal.org/project/config_navigator/issues/3616780)
- [#3616355: Add a training dataset of common configuration queries](https://www.drupal.org/project/config_navigator/issues/3616355)

---

## 6. Challenges and Learnings

The hardest part was working out what "good" natural language search actually means in Drupal. It was not just about getting a query to return results. It was deciding what information mattered, how to rank settings so the right one appeared first, and making the experience useful for someone who does not already understand Drupal's internal structure.

That question kept producing answers I had not expected. The index only covered /admin/config for most of the project, which meant content types, menus, taxonomy and permissions were invisible to the assistant. The proposal's own example question, "I need to add a new content type", could not be answered at all. Search was working correctly the whole time; what was wrong was the definition of which pages counted.

The other hard part was working within an existing, unfamiliar codebase and ecosystem. I had to learn how Drupal handles configuration, search, plugins, testing and contribution workflows while designing something that fits the project rather than just works in isolation. One bug came directly out of that: reading the route list to build the index triggered a router rebuild, which fired the module's own event subscriber, which started building the index again while the first build was still running. Every page ended up indexed twice. A test written days earlier caught it.

---

## Acknowledgements

Thank you to my mentor, Stephen Mustgrave, for the time he put into reviewing this work. His reviews consistently found the things I had not thought about rather than the things I had, and several of the module's better decisions came out of a question he asked rather than something I planned. The feedback recording setting exists because he asked whether people should be able to opt out of being recorded, which had not occurred to me.

He also gave me room to keep moving when the deadline got close, and offered to walk me through Drupal's review process afterwards, which is the part I am most looking forward to. Thank you as well to the Drupal community and to Google for running the program.

---

## Disclosure

AI assisted tools were used to support development and research on this module. All generated code was reviewed, tested and understood before being included.

---

*This report was created for the Google Summer of Code 2026 final evaluation.*
