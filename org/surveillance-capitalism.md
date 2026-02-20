# Surveillance Capitalism

## What it is

Shoshana Zuboff argues that big tech’s dominant business model isn’t “just advertising” or “just technology” — it’s an economic system she calls **surveillance capitalism**: companies **take private human experience**, turn it into **behavioral data**, use it to build **predictions of what you’ll do**, and sell those predictions (and the ability to influence outcomes) to paying customers. ([Harvard Gazette][1])

## Main points (the core idea, stripped down)

* **The raw material is you.** The “free” service isn’t really free; *you* are treated as free input. Zuboff frames this as companies claiming “private human experience” as a resource. ([Harvard Gazette][1])
* **Data → predictions → sales.** Behavioral data is processed into “prediction products” about what you’ll do now/soon/later, then sold in markets aimed at shaping outcomes (originally online ads, now much broader). ([Harvard Gazette][1])
* **It spreads beyond one company.** The UK Parliament report and Zuboff both emphasize this is a *systemic* model, not just “Facebook being bad.” ([UK Parliament][2])
* **Third-party apps are a major pipeline.** A WSJ investigation found popular apps (especially health/fitness) transmitting sensitive info to Facebook (e.g., weight, menstrual-cycle info) via integrations/SDKs. ([The Wall Street Journal][3])
* **“Mini scandals” are predictable.** If the business model depends on more volume + more variety of data, repeated privacy incidents aren’t accidents — they’re an expected consequence. ([The Wall Street Journal][3])
* **Behavior influence is the next step.** Zuboff’s key claim: the best predictions come from *intervening* — nudging/tuning behavior through interfaces, devices, and ubiquitous sensors (phones, “smart” devices, etc.). (This is her argument; evidence varies by case.) ([Democracy Now!][4])
* **Power = “asymmetry of knowledge.”** They learn a lot about you; you learn very little about how they operate, what they infer, or how systems steer attention and choices. ([Democracy Now!][4])

## What was “known” from the specific headlines referenced

* **UK Parliament “digital gangsters”**: The UK House of Commons Digital, Culture, Media and Sport Committee’s *Disinformation and ‘fake news’* final report (Feb 2019) used that phrase and argued for stronger statutory regulation and scrutiny of Facebook’s practices. ([UK Parliament][2])
* **Apps sharing sensitive data with Facebook**: WSJ reported health apps sending sensitive data; follow-on reporting described regulators seeking documents and apps changing behavior after scrutiny. ([The Wall Street Journal][3])
* **“Facebook planned to spy on Android users”**: Computer Weekly reported internal emails describing plans to use Android phone signals/location-related data to build “location-aware” products. ([Computer Weekly][5])
* **Onavo / “VPN as surveillance”**: Reporting around Onavo (and Facebook Research) described using VPN-like tooling to observe user behavior for competitive/market insight; Facebook later announced it would shut down Onavo Protect. ([TechCrunch][6])

## More recent related signal (shows the issue didn’t end)

* **Flo period-tracker case**: A jury found Meta violated California’s Invasion of Privacy Act relating to collection/interception of sensitive Flo app data (Aug 2025 reporting). ([The Verge][7])
* **Platform privacy moves get contested**: Apple’s App Tracking Transparency (ATT) reduced some cross-app tracking, but antitrust regulators (France, Italy) have fined Apple over how ATT was implemented, arguing competitive harms (2025). ([Reuters][8])

## Actionable insights (what you can actually do)

### If you’re an individual

* **Be extra strict with health, period, fertility, mental-health, and “tracker” apps.** Assume sensitive inputs may travel to third parties unless proven otherwise. Prefer apps with clear “no sharing” policies and minimal permissions. ([Axios][9])
* **Cut off the easiest pipelines:**

  * iPhone: keep **App Tracking Transparency** set to “Ask App Not to Track” / disallow tracking where possible (it helps, even if imperfect). ([Reuters][8])
  * Android: review **app permissions** (location, contacts, background access), and remove apps that don’t justify them (location especially). (General best practice; not a single-source claim.)
* **Assume “Login with Facebook/Google” increases linkage.** Use email-based logins when you can (reduces easy cross-service identity joining).
* **Use a privacy-hardened browser setup** (tracking protection + ad/tracker blocking). It won’t solve everything (SDK sharing still exists), but it reduces web tracking surface.

### If you’re building products (engineering/product/org level)

* **Data minimization isn’t optional anymore.** Collect only what you need, keep it briefly, and segregate sensitive data by design (especially health-related). The Flo case is a clear warning shot. ([The Verge][7])
* **Audit third-party SDKs** like you audit dependencies in security: know what they transmit, when, and under what identifiers. The WSJ reporting highlighted how “normal” SDK integrations can become sensitive-data exfil paths. ([The Wall Street Journal][3])
* **Treat “analytics” as a potential liability surface**, not just a growth tool. Build privacy-preserving measurement (aggregation, on-device processing, differential privacy where feasible) instead of user-level event streams.

### If you care about policy / governance

* **Don’t regulate only one company.** The UK committee framing and Zuboff’s thesis both point to the *business model* as the object of regulation (limits on collection, use, inference, and transfer), not just penalties after breaches. ([UK Parliament][2])
* **Make sensitive-data prohibitions real.** Health/biometric/location and inferences about them need hard limits (and meaningful enforcement), because “consent banners” are weak against complex data flows. ([The Wall Street Journal][3])

If you want, tell me your platform (iPhone/Android + which browsers) and the kinds of apps you rely on most (health? social? smart-home?), and I’ll give you a tight “highest impact / lowest hassle” privacy checklist tailored to your actual setup.

[1]: https://news.harvard.edu/gazette/story/2019/03/harvard-professor-says-surveillance-capitalism-is-undermining-democracy/?utm_source=chatgpt.com "Harvard professor says surveillance capitalism is ..."
[2]: https://publications.parliament.uk/pa/cm201719/cmselect/cmcumeds/1791/1791.pdf?utm_source=chatgpt.com "Disinformation and 'fake news': Final Report - Parliament UK"
[3]: https://www.wsj.com/articles/you-give-apps-sensitive-personal-information-then-they-tell-facebook-11550851636?gaa_at=eafs&gaa_n=AWEtsqeQw627l-rOexxWCkAOIDX1z7Cr1uiZOQ9i6FDMeZWAosvAxIrACK2E&gaa_sig=VOHXwzEinX3PHP7SSB60wkZuIgcA5dBfuUbLhdN_HXhipuzVGde6jBNMMDFCBMXcELPBGIqc9ZV_5H8xGAH_tw%3D%3D&gaa_ts=699782b1&utm_source=chatgpt.com "You Give Apps Sensitive Personal Information. Then They ..."
[4]: https://www.democracynow.org/2019/3/1/age_of_surveillance_capitalism_we_thought?utm_source=chatgpt.com "Age of Surveillance Capitalism: “We Thought We Were ..."
[5]: https://www.computerweekly.com/news/252458208/Facebook-planned-to-spy-on-Android-phone-users-internal-emails-reveal?utm_source=chatgpt.com "Facebook planned to spy on Android phone users, internal ..."
[6]: https://techcrunch.com/2019/02/21/facebook-removes-onavo/?utm_source=chatgpt.com "Facebook will shut down its spyware VPN app Onavo"
[7]: https://www.theverge.com/news/753469/meta-flo-period-tracker-lawsuit-verdict?utm_source=chatgpt.com "Meta illegally collected Flo users' menstrual data, jury rules"
[8]: https://www.reuters.com/technology/french-antitrust-regulator-fines-apple-150-million-euros-over-privacy-tool-2025-03-31/?utm_source=chatgpt.com "French antitrust regulator fines Apple 150 million euros over privacy tool"
[9]: https://www.axios.com/2019/02/23/facebook-tracks-personal-data-through-apps?utm_source=chatgpt.com "Facebook tracks highly personal details through apps"
