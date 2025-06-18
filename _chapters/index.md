---
title: "Getting Started"
description: "Introduction to C# for JavaScript and TypeScript developers"
chapter: 0
type: "chapter-index"
order: 0
category: "getting-started"
chapter_overview: "This chapter introduces C# concepts that will be familiar to JavaScript and TypeScript developers, setting the foundation for your migration journey."
estimated_time: 45
difficulty: "beginner"
lessons:
  - title: "Introduction to C# for JS/TS Developers"
    slug: "01-introduction"
    duration: 15
    description: "Complete migration guide covering C-type languages, syntax comparisons, and mental shifts needed"
  - title: "Environment Setup"
    slug: "02-environment-setup"
    duration: 20
    description: "Install .NET SDK, choose your IDE, and create your first C# project"
  - title: "Hello World and Basic Syntax"
    slug: "03-hello-world"
    duration: 10
    description: "Write your first C# program and understand basic syntax differences"
exercises:
  - title: "Setup Your Development Environment"
    slug: "01-environment-setup"
    duration: 15
    description: "Install .NET SDK and create a hello world console application"
  - title: "Basic Syntax Comparison"
    slug: "02-syntax-comparison"
    duration: 10
    description: "Convert simple JavaScript functions to C# methods"
prerequisites: []
learning_objectives:
  - "Set up a complete C# development environment"
  - "Understand fundamental differences between JS/TS and C#"
  - "Write and run your first C# application"
  - "Navigate the .NET CLI and basic tooling"
date: "2025-06-18"
status: "published"
featured: true
navigation:
  previous: null
  next: "/chapters/01-language-fundamentals/"
---

# Getting Started with C# for JavaScript/TypeScript Developers

Welcome to your journey from JavaScript/TypeScript to C#! This chapter will ease you into the C# world by building on concepts you already know from web development.

## Chapter Overview

This foundational chapter covers everything you need to start writing C# code, from understanding the language's philosophy to setting up your development environment and writing your first programs.

## What You'll Learn

By the end of this chapter, you'll be able to:

- **Understand C# fundamentals** - How C# differs from and relates to JavaScript/TypeScript
- **Set up your environment** - Install .NET SDK and choose the right development tools
- **Write basic C# code** - Create console applications and understand core syntax
- **Use .NET tooling** - Navigate the CLI and project structure

## Lessons in This Chapter

{% for lesson in page.lessons %}

### {{ forloop.index }}. [{{ lesson.title }}]({{ lesson.slug }})

_{{ lesson.duration }} minutes_

{{ lesson.description }}

{% endfor %}

## Hands-On Exercises

{% for exercise in page.exercises %}

### Exercise {{ forloop.index }}: [{{ exercise.title }}](exercises/{{ exercise.slug }})

_{{ exercise.duration }} minutes_

{{ exercise.description }}

{% endfor %}

## Prerequisites

{% if page.prerequisites.size > 0 %}
Before starting this chapter, you should have:
{% for prereq in page.prerequisites %}

- {{ prereq }}
  {% endfor %}
  {% else %}
  No specific prerequisites! This course assumes you have JavaScript or TypeScript experience and are ready to learn C#.
  {% endif %}

## Time Commitment

**Total estimated time:** {{ page.estimated_time }} minutes ({{ page.estimated_time | divided_by: 60.0 | round: 1 }} hours)

This includes all lessons, exercises, and time for experimentation. Feel free to go at your own pace!

## Getting Help

If you get stuck during this chapter:

1. **Check the code samples** in each lesson
2. **Review the exercises** - they reinforce key concepts
3. **Refer to the [troubleshooting guide](../resources/troubleshooting)**
4. **Ask questions** in the course discussion area

## Next Steps

After completing this chapter, you'll be ready to dive into [Chapter 1: Language Fundamentals]({{ page.navigation.next }}), where we'll explore C#'s type system, variables, and operators in detail.

---

_Ready to get started? Begin with [{{ page.lessons[0].title }}]({{ page.lessons[0].slug }})!_
