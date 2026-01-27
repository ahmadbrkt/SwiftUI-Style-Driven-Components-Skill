# SwiftUI Style-Driven Components Skill

Expert guidance for any AI coding tool that supports the [Agent Skills open format](https://agentskills.io/home) — building extensible SwiftUI components using Apple's style-driven architecture patterns.

Distilled from Apple's official patterns (`ButtonStyle`, `LabelStyle`, `ToggleStyle`) into actionable, concise references for agents.

## Who this is for
- Teams building reusable SwiftUI component libraries who want Apple-standard extensibility.
- Developers creating custom components that need multiple visual styles without code duplication.
- Anyone wanting to implement environment-based styling that cascades through view hierarchies.

## How to Use This Skill

### Option A: Using skills.sh (recommended)
Install this skill with a single command:
```bash
npx skills add https://github.com/ahmadbrkt/swiftui-style-driven-components-skill --skill swiftui-style-driven-components
```

For more information, visit the [skills.sh platform page](https://skills.sh/ahmadbrkt/SwiftUI-Style-Driven-Components-Skill/swiftui-style-driven-components).

Then use the skill in your AI agent, for example:  
> Use the swiftui style-driven components skill and create a Card component with style protocol support

### Option B: Claude Code Plugin

#### Personal Usage

To install this Skill for your personal use in Claude Code:

1. Add the marketplace:
   ```bash
   /plugin marketplace add ahmadbrkt/SwiftUI-Style-Driven-Components-Skill
   ```

2. Install the Skill:
   ```bash
   /plugin install swiftui-style-driven-components@swiftui-style-driven-components-skill
   ```

#### Project Configuration

To automatically provide this Skill to everyone working in a repository, configure the repository's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "swiftui-style-driven-components@swiftui-style-driven-components-skill": true
  },
  "extraKnownMarketplaces": {
    "swiftui-style-driven-components-skill": {
      "source": {
        "source": "github",
        "repo": "ahmadbrkt/SwiftUI-Style-Driven-Components-Skill"
      }
    }
  }
}
```

When team members open the project, Claude Code will prompt them to install the Skill.

### Option C: Manual install
1) **Clone** this repository.  
2) **Install or symlink** the `swiftui-style-driven-components/` folder following your tool's official skills installation docs (see links below).  
3) **Use your AI tool** as usual and ask it to use the "swiftui-style-driven-components" skill for component architecture tasks.

#### Where to Save Skills

Follow your tool's official documentation, here are a few popular ones:
- **Codex:** [Where to save skills](https://developers.openai.com/codex/skills/#where-to-save-skills)
- **Claude:** [Using Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview#using-skills)
- **Cursor:** [Enabling Skills](https://cursor.com/docs/context/skills#enabling-skills)

**How to verify**: 

Your agent should reference the triage/playbook in `swiftui-style-driven-components/SKILL.md` and jump into the relevant reference file for your component task.

## What This Skill Offers

This skill gives your AI coding tool comprehensive SwiftUI style-driven component guidance. It can:

### Guide Your Component Architecture
- Determine when to use style protocols vs simple components
- Structure files following Apple's organizational patterns
- Implement the Protocol → Configuration → Component → Environment chain
- Design type-safe APIs with internal type erasure

### Create Style Protocols
- Define protocols with `DynamicProperty` for environment access
- Use `@ViewBuilder @MainActor` for proper UI thread safety
- Create configuration types with nested type-erased views
- Follow Apple's `ButtonStyle`, `LabelStyle` conventions exactly

### Build Extensible Components
- Write components that read styles from environment
- Create configurations internally (never exposed to initializers)
- Wrap `style.makeBody` in `AnyView` for type erasure
- Support adding new styles without modifying existing components

### Implement Environment Styling
- Use `@Entry` macro for environment keys
- Store styles as existential types (`any ComponentStyle`)
- Create view modifiers for style application
- Enable subtree styling that cascades through hierarchies

### Apply Design System Integration
- Use design tokens instead of magic numbers
- Implement spacing, typography, and color systems
- Create consistent visual language across components
- Support theming through environment values

### Write Tests & Previews
- Organize snapshot tests for all style variants
- Structure previews for different configurations
- Test accessibility with Dynamic Type support
- Verify style isolation and environment propagation

## What Makes This Skill Different

**Apple-Native Patterns**: Follows exactly how Apple builds `ButtonStyle`, `LabelStyle`, `ToggleStyle`, and other style protocols. Your components will feel native to SwiftUI developers.

**Non-Opinionated**: Focuses on the structural pattern, not visual design. Works with any design system, component library, or project architecture.

**Practical Decision Trees**: Includes clear guidance on when to use style protocols and when to skip them. Not every component needs this pattern—the skill helps you decide.

**Complete Examples**: Every pattern includes full, compilable Swift code. No pseudo-code or incomplete snippets.

**Anti-Pattern Guidance**: Shows both correct (✅) and incorrect (❌) approaches with explanations of why patterns matter.

## Skill Structure

```
swiftui-style-driven-components/
├── SKILL.md                           # Main skill file with decision trees
└── references/
    ├── component-structure.md         # File organization and naming conventions
    ├── style-protocol.md              # Protocol definition and configuration pattern
    ├── environment-keys.md            # Environment injection and usage patterns
    ├── common-patterns.md             # Convenience initializers, parameterized styles
    ├── design-system.md               # Design tokens, typography, colors
    ├── testing.md                     # Snapshot tests and test organization
    ├── previews.md                    # Preview structure and checklist
    └── accessibility.md               # Labels, identifiers, Dynamic Type
```

## Contributing

Contributions are welcome! This repository follows the [Agent Skills open format](https://agentskills.io/home), which has specific structural requirements.

**We strongly recommend using AI assistance for contributions:**
- Use the [skill-creator skill](https://github.com/anthropics/skills/tree/main/skills/skill-creator) with Claude to ensure proper formatting
- This helps maintain the Agent Skills format and ensures your contribution works correctly with AI agents

**Please read [CONTRIBUTING.md](CONTRIBUTING.md) for:**
- How to use the skill-creator skill for contributions
- Agent Skills format requirements
- Quality standards and best practices
- Pull request process

This skill is maintained to reflect the latest SwiftUI style-driven component best practices and will be updated as the framework evolves.

## License

This skill is open-source and available under the MIT License. See [LICENSE](LICENSE) for details.
