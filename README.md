# Welcome

Hello👋! This is the repository housing content for learning DSC. The repository has multiple workflows to help get clean content in.

[![MarkdownLint](https://github.com/Gijsreyn/learn-dsc/actions/workflows/markdownLint.yaml/badge.svg?branch=main)](https://github.com/Gijsreyn/learn-dsc/actions/workflows/markdownLint.yaml)
[![Spell Checker](https://github.com/Gijsreyn/learn-dsc/actions/workflows/spellCheck.yaml/badge.svg?branch=main)](https://github.com/Gijsreyn/learn-dsc/actions/workflows/spellCheck.yaml)
[![MarkdownLink](https://github.com/Gijsreyn/learn-dsc/actions/workflows/markdownLink.yaml/badge.svg?branch=main)](https://github.com/Gijsreyn/learn-dsc/actions/workflows/markdownLink.yaml)

## What is DSC?

Learn DSC is a learning course you can follow to learn everything about Microsoft Desired State Configuration (v3). Inspired by other fellow content creators, this training platform helps both beginners and advanced users learn new skills in DSC v3. This version has been completely rewritten in Rust, moving away from its PowerShell foundation. Our structured learning path is a bit geeky and follows the Jedi-inspired ranking system, guiding you from the initiate level to Jedi Master.

If you are sitting in one of these roles:

- IT System Administrator
- DevOps Engineer
- Developer
- Solution Architect

This free training course is applicable for you.

## Getting started

Our website can be found at: <https://learndsc.guide>.

Start your journey by picking the level you're comfortable with:

- [Complete new to DSC](https://learndsc.guide/docs/initiate/)

## Feedback

If you have any questions, comments, or additional feedback, check out the open issues first. If you cannot find what you're looking for, please open an [issue](https://github.com/Gijsreyn/learn-dsc/issues)

## Roadmap

We're continuously improving Learn DSC with:

- Additional learning levels
- More hands-on exercises
- New resource examples
- Updated content for latest DSC releases

To find a list of features, either look in the [projects](https://github.com/Gijsreyn/learn-dsc/projects) section, or at the [issues](https://github.com/Gijsreyn/learn-dsc/issues) in this project.

## Contribution

We would love to have your first PR to update the content. Take a look at the [backlog](https://github.com/Gijsreyn/learn-dsc/projects) or [issues](https://github.com/Gijsreyn/learn-dsc/issues) that are open were you'd like to work on and we can always help you get started.

You can also simply contribute by:

- Adding new lessons or examples
- Creating lab exercises
- Update existing content for clarity
- Translating content

## Building the Website Locally

### Installing Hugo

Before running the website locally, you need to have Hugo installed on your system.

#### Windows

You can install Hugo using either WinGet or Chocolatey:

**Using WinGet:**

```powershell
# Install Hugo Extended (recommended for this project)
winget install Hugo.Hugo.Extended
```

**Using Chocolatey:**

```powershell
# Install Hugo Extended
choco install hugo-extended -y
```

**macOS:**

```sh
# Using Homebrew
brew install hugo
```

**Linux:**

```sh
# Using Snap
sudo snap install hugo
```

To run the website locally for development:

```sh
# Clone the repository
git clone https://github.com/gijsreyn/learn-dsc.git
cd learn-dsc

# Install Hugo modules
hugo mod get

# Start the local server
hugo server -D
```

Your local copy will be available at <http://localhost:1313/>

## License

Distributed under the MIT License. See [LICENSE](/LICENSE) for more information.
