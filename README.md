<div align="center">
  <img src="https://developers.google.com/open-source/gsoc/resources/downloads/GSoC-logo-horizontal.svg" alt="Google Summer of Code Logo" width="400" style="margin-bottom: 20px;">
  <h1>Google Summer of Code 2026 - Final Report</h1>
  <p><strong>Project:</strong> AI-Powered Configuration Navigator</p>
</div>

---

**Contributor:** Akash Deep  
**Organization:** Drupal  
**Mentor:** Stephen Mustgrave  
**Project Link:** [Config Navigator on Drupal.org](https://www.drupal.org/project/config_navigator)

---

## 1. Project Overview & Goals

**Config Navigator** is a tool for Drupal that lets you find and understand admin settings by asking questions in plain language. Instead of digging through menus and documentation, you ask what setting you need and it takes you there.

Drupal has a huge number of configuration settings, and finding the right one is difficult if you don't already know where it lives. The information already exists in Drupal, it just isn't easy to navigate. An admin shouldn't need to know Drupal's menu structure or terminology to answer something like "where do I change the site logo?". Natural language search makes that much more intuitive.

The core objectives were:
*   Build a chat assistant with a persistent help icon on admin pages, able to understand configuration questions asked in plain language.
*   Take the person straight to the right page, with the relevant setting highlighted on arrival.
*   Support follow-up questions, so a conversation can build on what was already asked.
*   Handle fuzzy matching, synonyms and Drupal-specific terminology, so the wording of a question does not have to match the wording of a page.
*   Suggest pages based on where the person already is and what their role allows them to see.
*   Store a configuration index holding each page's purpose and keywords, kept up to date automatically.
*   Integrate with the **Drupal AI ecosystem** (`drupal/ai`) for language understanding, while remaining useful without it.
*   Provide RESTful endpoints and a plugin API so other modules can integrate and register their own configuration pages.
*   Collect feedback on answers and use it to improve later ones.

---

## 2. Work Accomplished

Over the course of the program the module went from nothing to a working assistant covering every deliverable in the proposal. The work was run entirely through Drupal's issue queue, one issue and one merge request at a time.

### The Assistant and Its Interface
*   **Chat Widget:** Built a persistent help icon on admin pages opening into a chat panel, with output escaped as text nodes so untrusted content can never render as HTML. ([Issue #3606897](https://www.drupal.org/project/config_navigator/issues/3606897), [#3615309](https://www.drupal.org/project/config_navigator/issues/3615309))
*   **Natural Language Matching:** Integrated with the Drupal AI module to interpret questions, returning a structured answer naming the page, its path and the specific setting where one can be identified. ([Issue #3606895](https://www.drupal.org/project/config_navigator/issues/3606895))
*   **Conversation Context:** Added follow-up support so a question like "what else can I change there?" resolves against the previous answer, with history stored per user so context survives a reload or a new visit. ([Issue #3606898](https://www.drupal.org/project/config_navigator/issues/3606898), [#3615631](https://www.drupal.org/project/config_navigator/issues/3615631))
*   **Visual Highlights:** After navigating from the assistant, the destination page shows a banner naming the question and highlights the relevant field. ([Issue #3615630](https://www.drupal.org/project/config_navigator/issues/3615630))
*   **Contextual Suggestions:** Opening the assistant suggests pages relevant to the admin area the person is already in, filtered to what their role can access. ([Issue #3615629](https://www.drupal.org/project/config_navigator/issues/3615629))
*   **Related Pages and Impact Warnings:** Answers carry a short set of related configuration pages, and a warning on pages where a change has wide effects such as clearing caches or altering text formats. ([Issue #3616356](https://www.drupal.org/project/config_navigator/issues/3616356), [#3616361](https://www.drupal.org/project/config_navigator/issues/3616361))

### Search and the Configuration Index
*   **Index Service:** Built the service that reads the site's routes and stores the configuration pages found, along with keyword search over it as a fallback for when AI is unavailable. ([Issue #3601652](https://www.drupal.org/project/config_navigator/issues/3601652), [#3606894](https://www.drupal.org/project/config_navigator/issues/3606894))
*   **Fuzzy Matching and Synonyms:** Search now tries the phrase as a whole, then splits it into words with common ones dropped and each expanded by known synonyms, ranked by how many terms a page matches, then falls back to spelling distance so typos still find something. ([Issue #3615633](https://www.drupal.org/project/config_navigator/issues/3615633))
*   **Purposes and Keywords:** Added columns holding what each page is for and the terms it should be found by, so a page can be found by a word its title never contains. The site email address lives on *Basic site settings*, whose title says nothing about email. ([Issue #3616359](https://www.drupal.org/project/config_navigator/issues/3616359))
*   **Help Text as Descriptions:** Used each module's own `hook_help()` text as a page description, so descriptions come from documentation that already ships with the site. ([Issue #3616363](https://www.drupal.org/project/config_navigator/issues/3616363))
*   **Index Scope:** Widened the index beyond `/admin/config` to cover structure, appearance, modules, permissions and roles, while skipping route templates and untitled pages. ([Issue #3616780](https://www.drupal.org/project/config_navigator/issues/3616780))
*   **Automatic Rebuilding:** The index now rebuilds whenever the router does, covering module installs, uninstalls and cache clears, with cron as a backstop for pages registered by other modules. ([Issue #3616343](https://www.drupal.org/project/config_navigator/issues/3616343))

### Integration, API and Performance
*   **RESTful Endpoints:** Added POST endpoints for chat, search, navigation, suggestions, feedback and clearing history, each requiring a CSRF token and returning proper status codes rather than a 200 with an error payload. ([Issue #3615632](https://www.drupal.org/project/config_navigator/issues/3615632))
*   **Plugin API:** Built a `ConfigNavigatorSource` plugin type so contrib modules can register their own configuration pages, including purposes and keywords, and be found by the assistant. ([Issue #3606900](https://www.drupal.org/project/config_navigator/issues/3606900))
*   **Caching:** Cached AI answers per question and conversation, tagged so a rebuilt index invalidates anything that might now point at a page which no longer exists. ([Issue #3615315](https://www.drupal.org/project/config_navigator/issues/3615315))

### Privacy, Permissions and the Feedback Loop
*   **Permission-Aware Results:** Results are filtered against the current user's access, so nobody is shown a page they cannot open. ([Issue #3606896](https://www.drupal.org/project/config_navigator/issues/3606896))
*   **Dedicated Permission:** Added a `use config navigator` permission so editors and other roles can use the assistant without needing full configuration access, which is what makes the filtering above meaningful. ([Issue #3616345](https://www.drupal.org/project/config_navigator/issues/3616345))
*   **Data Sent to Providers:** The question is searched first and only matching pages are sent to the AI provider, never the full index. A setting turns AI matching off entirely for sites that will not send anything externally. ([Issue #3616362](https://www.drupal.org/project/config_navigator/issues/3616362))
*   **Feedback and Learning:** Added thumbs up and down on answers, with a setting to turn recording off, and fed confirmed answers back into later prompts as examples. ([Issue #3606899](https://www.drupal.org/project/config_navigator/issues/3606899), [#3616360](https://www.drupal.org/project/config_navigator/issues/3616360))

### Quality Assurance
*   **Test Coverage:** Kernel, functional and functional JavaScript tests across the index service, AI matching, the endpoints, permissions, caching, history and the widget itself, all running in Drupal GitLab CI. ([Issue #3613351](https://www.drupal.org/project/config_navigator/issues/3613351), [#3613975](https://www.drupal.org/project/config_navigator/issues/3613975))
*   **Training Dataset:** Committed a fixture of twenty realistic questions with the page each should find, and a test that measures how many resolve, so search quality is a number that can be watched rather than an impression. ([Issue #3616355](https://www.drupal.org/project/config_navigator/issues/3616355))
*   **Documentation:** Wrote the README covering installation, provider setup, the settings, permissions, the API and how another module registers its own pages. ([Issue #3606901](https://www.drupal.org/project/config_navigator/issues/3606901))

---

## 3. The Current State

The module is functional and installable from the `1.0.x` branch, and every deliverable set out in the proposal is built. Thirty issues were opened over the course of the project, and every merge request runs the full Drupal GitLab CI pipeline covering coding standards, spell checking and the test suite.

Search quality was measured against the training dataset, twenty questions phrased the way a site builder would actually ask them, each paired with the page that answers it. The number moved as the work landed:

| Stage | Questions resolved |
| :--- | :--- |
| Whole-phrase matching only | 0 of 20 |
| With tokenized search and synonyms | 9 of 20 |
| With the index widened beyond `/admin/config` | 16 of 20 |
| With page keywords and purposes as well | **20 of 20** |

There is no tagged release yet.

---

## 4. What's Left to Do (Future Scope)

*   **Merging the Remaining Work:** A number of merge requests are still in review. Landing those is the first priority, since everything else depends on the module being in one piece.
*   **User Testing:** This is the part I would most want to do next. I would want to see how people with different levels of Drupal experience actually phrase their questions, where the search gives useful results and where it gives misleading ones, and use that to improve matching and ranking. The training dataset gives a place to record what comes out of it, since a question someone asked and the assistant answered badly can go straight into the fixture.
*   **A Tagged Release:** Once the remaining work is merged and anything surfaced by testing is cleaned up, a first release would make the module available for wider use.

---

## 5. Code Contributions

All code developed during the GSoC period has been committed to the project's official repository on Drupal.org.

*   **Project URL:** <https://www.drupal.org/project/config_navigator>
*   **Full Commit History:** [View Commits on GitLab](https://git.drupalcode.org/project/config_navigator/-/commits/1.0.x)
*   **Issue Queue:** [View all issues](https://www.drupal.org/project/issues/config_navigator)
*   **Merge Requests:** [View all merge requests](https://git.drupalcode.org/project/config_navigator/-/merge_requests)

Thirty issues and twenty-five merge requests over the course of the project.

**Highlighted Issues:**

*   [#3606900: Add plugin API for contrib module config registration](https://www.drupal.org/project/config_navigator/issues/3606900)
*   [#3615632: Add RESTful endpoints for chat, search, and navigation](https://www.drupal.org/project/config_navigator/issues/3615632)
*   [#3616362: Avoid sending unnecessary site data to external AI providers](https://www.drupal.org/project/config_navigator/issues/3616362)
*   [#3616780: Index configuration pages outside /admin/config](https://www.drupal.org/project/config_navigator/issues/3616780)
*   [#3616355: Add a training dataset of common configuration queries](https://www.drupal.org/project/config_navigator/issues/3616355)

---

## 6. Challenges and Learnings

The hardest part was working out what "good" natural language search actually means in Drupal. It was not just about getting a query to return results. It was deciding what information mattered, how to rank settings so the right one appeared first, and making the experience useful for someone who does not already understand Drupal's internal structure.

That question kept producing answers I had not expected. The index only covered `/admin/config` for most of the project, which meant content types, menus, taxonomy and permissions were invisible to the assistant. The proposal's own example question, *"I need to add a new content type"*, could not be answered at all. Search was working correctly the whole time; what was wrong was the definition of which pages counted.

The other hard part was working within an existing, unfamiliar codebase and ecosystem. I had to learn how Drupal handles configuration, search, plugins, testing and contribution workflows while designing something that fits the project rather than just works in isolation. One bug came directly out of that: reading the route list to build the index triggered a router rebuild, which fired the module's own event subscriber, which started building the index again while the first build was still running. Every page ended up indexed twice. A test written days earlier caught it.

---

## Acknowledgements

Thank you to my mentor, **Stephen Mustgrave**, for the time he put into reviewing this work. His reviews consistently found the things I had not thought about rather than the things I had, and several of the module's better decisions came out of a question he asked rather than something I planned. The feedback recording setting exists because he asked whether people should be able to opt out of being recorded, which had not occurred to me.

He also gave me room to keep moving when the deadline got close, and offered to walk me through Drupal's review process afterwards, which is the part I am most looking forward to. Thank you as well to the Drupal community and to Google for running the program.

---

## Disclosure

AI assisted tools were used to support development and research on this module. All generated code was reviewed, tested and understood before being included.

---

*This report was created for the Google Summer of Code 2026 final evaluation.*
