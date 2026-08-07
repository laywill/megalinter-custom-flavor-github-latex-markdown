# Draft: upstream question

Not yet filed. Intended for
[oxsecurity/megalinter](https://github.com/oxsecurity/megalinter/issues/new?template=general-question.md),
using the **General question** template.

Suggested title: *What licence should custom flavor repositories use? Worth a
line in the Custom Flavors docs*

Everything below the rule is the issue body, ready to paste. Delete this file
once the issue is filed.

---

Hello, and thanks for MegaLinter — the custom flavors feature has cut our CI
image pull time considerably.

While setting one up I hit a question the docs don't currently answer: **what
licence should a custom flavor repository carry?**

MegaLinter is AGPL-3.0. A generated flavor repo contains no MegaLinter source,
but it does contain files produced from the generator's own templates
(`action.yml`, the two workflows, the README), and it publishes a container
image that bundles MegaLinter itself. So it wasn't obvious to me whether:

- the generated scaffolding is considered a derivative work of the AGPL-3.0
  templates,
- publishing the image to a registry counts as conveying AGPL-3.0 software and
  brings the corresponding-source obligation with it, and
- flavor authors are expected to license their repo AGPL-3.0 to match, or are
  free to choose.

Looking at the existing flavor repos on GitHub, there doesn't seem to be a
settled convention: most have no licence file at all (including
`nvuillam/megalinter-custom-flavor-npm-groovy-lint` and
`nvuillam/megalinter-custom-flavor-demo`), and a couple use CC0-1.0. I couldn't
find any using AGPL-3.0.

I've gone with AGPL-3.0 for ours, as the conservative option, and noted in the
README that the published image bundles MegaLinter with a link to upstream
source. But I'd rather follow your intent than guess.

Would you consider adding a short note to the
[Custom Flavors page](https://megalinter.io/latest/custom-flavors/) — even a
single sentence on what licence you expect or recommend for flavor repos? I
suspect most flavor authors haven't thought about it at all, which is arguably
the bigger issue than whichever answer is correct.

Happy to send a docs PR with whatever wording you'd like.

To be clear, I'm not asking for legal advice, just for the project's intent.
