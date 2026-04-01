# Prompt: Build the AiGate Landing Page

## Who you are

You are an expert frontend developer and conversion-focused designer. You are building a landing page for an open-source developer tool.

## What AiGate is

AiGate is a local CLI tool that prevents developers from accidentally leaking secrets (API keys, database credentials, private keys, etc.) into AI coding assistants like Claude Code, Cursor, and Copilot.

It works two ways:
1. **Hook mode (recommended):** Installs as a Claude Code hook that scans every prompt and tool input before it's sent. Zero config, no proxy, one command to install.
2. **Proxy mode:** Runs as a transparent HTTP proxy on localhost that intercepts outbound requests to AI provider APIs, scans the prompt content, and blocks/warns if secrets are found.

It is 100% local — no cloud, no accounts, no telemetry. Just `pip install aigate` and go.

**Key stats from stress testing:**
- Scans a typical prompt in under 0.2ms
- Zero false positives across 12 common code patterns
- Detects 8 secret categories: AWS keys, database URLs, private keys, API tokens (OpenAI, GitHub, GitLab, Slack, Anthropic, SendGrid, Square), env file contents, GCP service accounts, Tailscale keys, and high-entropy strings near sensitive keywords
- 3 modes: block (reject the request), warn (forward + log), audit (silent logging)
- ~900 lines of Python. Zero runtime dependencies beyond mitmproxy, click, and pyyaml

## The origin story / hook

On March 31, 2026, LAPSUS$ breached Mercor AI by exploiting production credentials a developer pasted into Claude. Same week: Axios npm supply chain attack (83M weekly downloads), Claude Code source leak, FBI director's email hacked by Iran. The common thread: **AI tools are an unmonitored exfiltration surface.**

Every company using AI coding assistants has developers pasting secrets into third-party APIs daily. No inspection layer exists between the developer's clipboard and the model. AiGate is that layer.

## Target audience

Engineering leads and individual developers at AI-native startups (10-200 engineers) who:
- Use Claude Code, Cursor, Copilot, or direct API calls daily
- Handle production infrastructure and have access to real credentials
- Don't have a security team or enterprise DLP
- Just read about the Mercor breach and are thinking "that could be us"

## Competitive positioning

| Competitor | What they do | Why AiGate is different |
|---|---|---|
| GitGuardian | Scans git commits for secrets | Catches secrets AFTER commit. AiGate catches them BEFORE they leave the machine. |
| Prompt Security | Protects AI apps from prompt injection | Different threat direction — AiGate protects companies from their own developers. |
| Lakera | LLM firewall (input/output filtering) | Focused on model safety, not credential leakage. |
| Nightfall | DLP for SaaS | Generic DLP, not built for AI prompt structure. Expensive. Enterprise sales cycle. |
| Zscaler/Netskope | Network-level DLP | Can't parse AI API request bodies. Block entire domains but can't selectively scan prompts. |

**AiGate's wedge:** The only tool that sits at the exact point where secrets leak into AI tools — after the developer types but before the API call fires. Purpose-built for the AI coding workflow.

## What to build

Build a single-page landing page for AiGate. The page should be a single HTML file with inline CSS and inline JavaScript (no external dependencies, no build step, no framework). It must look polished, modern, and professional — the kind of page a developer would trust.

### Tech constraints

- **Single `index.html` file** — all CSS and JS inline
- **No external dependencies** — no CDN links, no Google Fonts, no Tailwind CDN, no frameworks
- **System font stack** — use `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif` for body and `"SF Mono", "Fira Code", "Fira Mono", Menlo, Consolas, "DejaVu Sans Mono", monospace` for code
- **Responsive** — must work on mobile, tablet, and desktop
- **Dark mode by default** — developers prefer dark themes. Use a dark background (#0a0a0f or similar deep navy/black) with light text. Accent color: electric blue (#3b82f6) or similar
- **No images** — use only CSS, SVG inline icons, Unicode characters, and CSS gradients/effects
- **Smooth scroll** between sections
- **Accessible** — proper heading hierarchy, sufficient contrast, focus states, semantic HTML

### Visual style

- Clean, minimal, developer-tool aesthetic. Think Linear, Warp, Raycast landing pages.
- Generous whitespace. Content should breathe.
- Subtle CSS animations for polish — fade-in on scroll, gentle hover effects on cards, a subtle gradient shimmer on the hero headline. Nothing flashy or distracting.
- Code blocks should look like a real terminal — dark background, syntax-highlighted (use colored spans, not a library), monospace font, with a macOS-style title bar (three dots: red/yellow/green).
- Cards with subtle borders or glow effects, not heavy shadows.
- Section transitions should feel seamless — avoid hard visual breaks between sections.

### Page structure and content

Build the page with these sections in order:

---

#### 1. Nav bar (sticky)

- Left: "AiGate" wordmark in bold monospace + a small shield icon (inline SVG)
- Right: nav links — "Features", "How it works", "Install" (smooth scroll to sections) + a "GitHub" link (href="#" placeholder) styled as a subtle outline button
- Compact, blurs the background slightly on scroll (backdrop-filter)

---

#### 2. Hero section

**Headline:** "Your AI assistant shouldn't know your secrets."

**Subheadline:** "AiGate scans every prompt before it leaves your machine. AWS keys, database credentials, private keys — caught and blocked in under 1ms. Open source. Fully local. Zero config."

**Two CTA buttons:**
- Primary (filled, blue): "Install in 30 seconds" → scrolls to install section
- Secondary (outline): "See how it works" → scrolls to how-it-works section

**Below the CTAs:** A terminal-style window showing a realistic demo:

```
$ echo "connect to postgres://admin:s3cret@prod-db.internal:5432/app" | aigate scan -

  🚨 1 secret(s) found in <stdin>:

  [database_urls] post****...****l/app (offset 11)

$ echo "Help me optimize this SQL query" | aigate scan -

  ✅ No secrets found in <stdin>
```

Style this terminal with a macOS window chrome (title bar with three colored dots, slightly rounded corners, subtle shadow). Make the output feel real — use appropriate colors for the emoji, rule name in a muted color, the redacted match in white/bold.

---

#### 3. The problem section

**Section title:** "The security gap nobody talks about"

Three cards in a row (stack on mobile):

**Card 1 — "Paste and pray"**
"Developers paste .env files, connection strings, and API keys into AI tools dozens of times a day. There's no inspection layer between your clipboard and a third-party API."

**Card 2 — "DLP doesn't understand AI"**
"Network-level DLP can block api.anthropic.com entirely, but it can't read inside a JSON request body to find the AWS key buried in message 47 of a multi-turn conversation."

**Card 3 — "Git scanning is too late"**
"GitGuardian catches secrets after they're committed. But when you paste a key into Claude, it's already on Anthropic's servers. There's no git push to intercept."

---

#### 4. How it works section

**Section title:** "Two lines. That's the setup."

Show two installation paths as tabs or toggle:

**Tab 1: Claude Code hook (recommended)**

Terminal window:
```
$ pip install aigate
$ aigate install-hook

  🛡️ AiGate Claude Code integration installed:

     Installed ~/.claude/hooks/aigate-scan-prompt.sh
     Installed ~/.claude/hooks/aigate-scan-tool.sh
     Added UserPromptSubmit hook to settings.json
     Added PreToolUse hook to settings.json

  Done. AiGate will now scan all Claude Code prompts and
  tool inputs for secrets automatically.
```

Below: "That's it. Every prompt and tool input is now scanned before Claude sees it. No proxy, no env vars, no certificates."

**Tab 2: Proxy mode**

Terminal window:
```
$ pip install aigate
$ aigate start

  🛡️ AiGate v0.1.0
     Mode:      block
     Proxy:     http://127.0.0.1:8080
     Providers: api.anthropic.com, api.openai.com, api.mistral.ai

  Configure your AI tool:
     export HTTPS_PROXY=http://127.0.0.1:8080
```

Below: "Works with any AI tool — Cursor, Copilot, direct API calls. Transparent interception with mitmproxy."

---

#### 5. What it catches section

**Section title:** "8 high-signal detection rules. Zero false positives."

Display as a 2x4 grid of compact cards (2 columns on desktop, 1 on mobile). Each card shows:
- Rule name
- A one-line description
- A small code example of what it catches

The 8 rules:

1. **AWS Access Keys** — Detects AKIA-prefixed credentials — `AKIA3EXAMPLE7KEY1`
2. **Database URLs** — Connection strings with embedded passwords — `postgres://admin:s3cret@host/db`
3. **Private Keys** — RSA, EC, DSA, PGP key blocks — `-----BEGIN RSA PRIVATE KEY-----`
4. **API Tokens** — OpenAI, GitHub, GitLab, Slack, Anthropic, SendGrid keys — `sk-proj-...`, `ghp_...`
5. **Env File Contents** — Sensitive variable assignments — `DATABASE_URL=postgres://...`
6. **GCP Service Accounts** — Service account JSON with private keys — `"type": "service_account"`
7. **Tailscale Keys** — Auth and API keys — `tskey-auth-...`
8. **High-Entropy Secrets** — Passwords and tokens detected by Shannon entropy — `password = "a8f3kL9m..."`

---

#### 6. Three modes section

**Section title:** "Block, warn, or watch. Your call."

Three columns (stack on mobile), each describing a mode:

**Block mode** (icon: shield with X)
"Rejects the request with a clear error. The AI tool sees a 400 response explaining exactly what was found and where. The developer removes the secret and retries. 10 seconds of friction that prevents a breach."

**Warn mode** (icon: warning triangle)
"Forwards the request but logs a warning. Use this when you're rolling out AiGate and want to understand your exposure before enforcing."

**Audit mode** (icon: eye)
"Silent logging only. No blocking, no warnings. Writes every detection to a JSON audit log. Perfect for compliance: prove to auditors that you're monitoring AI tool usage."

---

#### 7. Technical details section (for the skeptical developer)

**Section title:** "Under the hood"

A clean, compact list of technical points:

- **~900 lines of Python.** Read the entire codebase in 20 minutes.
- **Sub-millisecond scanning.** Typical prompts scan in 0.005ms. Even 1MB of text scans in 67ms. The 5-second hook timeout will never be hit.
- **Regex + entropy. No ML.** Deterministic, auditable, no model downloads. Shannon entropy analysis catches high-entropy strings near keywords like `password`, `secret`, `token`.
- **Allowlist with wildcards.** Suppress known false positives with fnmatch patterns: `sk-test-*` for Stripe test keys, `AKIAIOSFODNN7EXAMPLE` for AWS docs.
- **JSON audit log.** Every detection is logged with ISO 8601 timestamp, provider, rule, redacted match, and action taken.
- **Zero telemetry. Zero cloud.** Nothing leaves your machine. No accounts, no API keys, no analytics. Verify it yourself — the source is right there.

---

#### 8. Install section (the final CTA)

**Section title:** "Install in 30 seconds"

Large terminal block:
```
pip install aigate
aigate install-hook    # For Claude Code (recommended)
# or
aigate start           # Proxy mode for any AI tool
```

Below the terminal: "Works on macOS and Linux. Python 3.11+ required."

Then a row of two buttons:
- Primary: "View on GitHub" (href="#" placeholder)
- Secondary: "Read the docs" (href="#" placeholder)

---

#### 9. Footer

Minimal footer with:
- "AiGate" wordmark
- "Open source under MIT license"
- Links: GitHub, Documentation, Report an issue (all href="#" placeholders)
- "Built because AI tools shouldn't know your secrets."

---

### Interaction details

- **Smooth scroll:** All nav links and CTAs that point to sections should smooth-scroll.
- **Scroll-triggered fade-ins:** Each section should fade in and slide up slightly (20px) when it enters the viewport. Use `IntersectionObserver` — no scroll event listeners.
- **Tab switching:** The "How it works" section tabs should switch content without page reload. Simple JS toggle with a subtle fade transition.
- **Terminal typing effect:** Optional (do it if it's tasteful) — the hero terminal could have a subtle cursor blink. Do NOT do a full typing animation; it's slow and annoying. Just a static terminal with a blinking cursor at the end.
- **Nav highlight:** As the user scrolls, highlight the active section's nav link.
- **Copy button on code blocks:** Add a small "copy" button to the install section terminal that copies the install commands to clipboard. Show a brief "Copied!" tooltip.

### Content tone

- Direct, confident, slightly urgent. Not salesy or corporate.
- Speak developer-to-developer. No buzzwords ("leverage", "empower", "unlock").
- Use concrete numbers: "under 1ms", "8 patterns", "~900 lines of code", "zero false positives".
- The underlying message: "You're already leaking secrets. This fixes it in 30 seconds."
- Short sentences. Short paragraphs. Let the product speak for itself.

### Things to avoid

- Don't use gradients on text (hard to read, looks cheap)
- Don't use parallax scrolling
- Don't add a cookie banner, email signup, or newsletter form
- Don't add pricing — this is open source
- Don't use stock illustrations, abstract shapes, or decorative SVGs that don't convey information
- Don't make the page longer than it needs to be — every section must earn its space
- Don't use animations that delay content visibility — fade-ins should be fast (300ms)
- Don't add a hamburger menu on mobile — just stack the nav links or use a minimal horizontal scroll

### Quality bar

The page should feel like it was built by the kind of developer who would build a tool like AiGate — someone who cares about craft, keeps things minimal, and doesn't add unnecessary complexity. If a section doesn't make someone more likely to install the tool, cut it.

When you're done, the page should work by opening `index.html` directly in a browser — no server, no build step, no dependencies.
