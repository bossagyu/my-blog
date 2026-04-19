+++
title = 'Three Models of Technical Proficiency: Dreyfus, Shu-Ha-Ri, and the Four Stages of Competence'
description = 'Compare three frameworks for technical proficiency—the Dreyfus model, Shu-Ha-Ri, and the Four Stages of Competence—and explore their characteristics and applications.'
date = 2026-04-19T00:00:00+09:00
draft = false
categories = ["Blog"]
tags = ["Learning", "Mastery", "Dreyfus", "Shu-Ha-Ri"]
+++

## Overview

The process of acquiring a skill is rarely uniform. We start by relying on manuals, then learn to judge based on context, and eventually reach a stage where the right action comes without conscious thought. To capture these gradual changes, many models have been proposed over the decades.

This article introduces three representative frameworks for technical proficiency—the **Dreyfus model**, **Shu-Ha-Ri**, and the **Four Stages of Competence**—and compares their origins, stages, and characteristics. The goal is to offer a toolkit for understanding where we are, designing our next step, and supporting the learning of others.

## Why Understand the Stages of Proficiency

Thinking of proficiency as a set of stages provides three main benefits.

First, **self-awareness**. Knowing which stage you are in clarifies what to work on next. The skills that matter for a beginner (executing procedures correctly) differ greatly from those that matter for a mid-level practitioner (judgment in context). Awareness of stages prevents misdirected effort.

Second, **learning design**. Rather than chasing a vague final goal, stage-based milestones make progress easier to sustain and measure. Many models also suggest that the optimal way to learn changes from stage to stage.

Third, **understanding others**. In mentoring or team leadership, estimating someone's current stage helps you choose the right support and level of challenge.

That said, models are not reality. Real learning is continuous and often uneven across domains. The frameworks below are best used as guideposts for thinking, not as rigid categorizations.

## The Dreyfus Model

### Origin and Background

The Dreyfus model was proposed in 1980 by brothers Stuart E. Dreyfus and Hubert L. Dreyfus at UC Berkeley, in a report titled "A Five-Stage Model of the Mental Activities Involved in Directed Skill Acquisition." Commissioned by the U.S. Air Force Office of Scientific Research, the report drew on pilot training, chess players, drivers, and other skilled practitioners.

Stuart specialized in operations research while Hubert specialized in philosophy (phenomenology). Together they formalized a key insight: the judgments of experts often cannot be reduced to explicit rules.

The model later became popular in software engineering thanks to books such as Andy Hunt's *Pragmatic Thinking and Learning*, and has become a shared vocabulary for discussing engineer growth.

### Five Stages

The Dreyfus model describes the journey from novice to expert in five stages.

```mermaid
flowchart LR
    A[Novice] --> B[Advanced Beginner]
    B --> C[Competent]
    C --> D[Proficient]
    D --> E[Expert]
```

**1. Novice**
Detaches from context and follows given rules. Following the rules is itself challenging, leaving little room to see exceptions or the bigger picture. Think of measuring ingredients from a recipe, or operating the pedals and wheel by the manual when learning to drive.

**2. Advanced Beginner**
Begins to notice contextual features through experience, but cannot yet distinguish important features from minor ones, and treats them with equal weight. This is the stage where learners ask many questions and encounter situations their rules don't cover.

**3. Competent**
Selects important information from a pool of signals and can set goals and plans on their own. Rules have become internalized. At the same time, learners begin to sense that "there is no single right answer," and may feel the weight of decision-making.

**4. Proficient**
Grasps situations holistically. Pattern recognition from past similar cases comes first; detailed rule-based reasoning fills in afterward. This is a hybrid stage where intuition and analysis work together.

**5. Expert**
Arrives at answers intuitively, embedded in the situation itself, without explicit rule-following. A seasoned doctor estimates a diagnosis at a glance; a master player narrows candidate moves before deliberate analysis. However, when the environment is unusual, even experts fall back on deliberate analysis.

### Cognitive Shifts Between Stages

The key insight of this model is that as stages progress, the center of cognition shifts from **analysis** to **pattern recognition** to **intuition**. This is not merely a speed-up but a qualitative change in what the practitioner perceives.

Moving up requires more than rule-following—it requires experience in interpreting rules in context. The Dreyfus brothers repeatedly note that mastery is tacit knowledge that textbooks alone cannot teach.

## Shu-Ha-Ri

### Origin and Background

Shu-Ha-Ri (守破離) is a Japanese model of training stages that emerged from traditional arts such as martial arts, tea ceremony, and flower arrangement. It appears explicitly in *Fuhaku Hikki*, a text by the Edo-period tea master Kawakami Fuhaku (1719-1807). Its roots trace back further, to Zeami's (1363-1443) *Fūshikaden* and its idea of *Jo-Ha-Kyū*, and to the teachings of Sen no Rikyū.

A poem often attributed to Rikyū reads, "Keep the forms of rules and manners until you break them, and even when you leave them, do not forget the origin." This line neatly captures the spirit of Shu-Ha-Ri: preserve the form, then break it, then transcend it—without losing its root.

In recent years, Shu-Ha-Ri has been applied to Western practices such as agile development, Scrum adoption, and design pattern learning, becoming a shared vocabulary among Japanese-speaking software professionals.

### Three Stages

Shu-Ha-Ri is a three-stage model built on a master-apprentice relationship.

```mermaid
flowchart LR
    A[Shu<br/>Follow the form] --> B[Ha<br/>Break the form]
    B --> C[Ri<br/>Leave the form]
    C -.new form.-> A
```

**Shu (守)**
Practice the forms taught by the master faithfully, without adding personal interpretation. The goal is to imprint the form onto body and mind. The "why" often comes later; what matters first is precise repetition.

**Ha (破)**
Building on the forms absorbed in Shu, the learner incorporates forms from other schools and personal adaptations, breaking the form. This is not mere rebellion but thoughtful comparison and testing. Here, the learner finally feels the reason behind the form.

**Ri (離)**
Leave the form and reach an independent mode of being. This is not discarding the form—by now, the form has become part of the self. Externally, the practitioner appears free and unconstrained. The phrase "do not forget the origin" warns that even at Ri, the root remains in Shu.

### Differences from Western Models

Where the Dreyfus model describes changes in an individual's cognition, Shu-Ha-Ri presumes that **forms are passed within a community**. The form encodes the history of the school and the wisdom of predecessors; inheriting it accurately is the purpose of Shu.

Shu-Ha-Ri also describes how the learner's **agency** changes. Reception dominates in Shu, dialogue and testing in Ha, creation in Ri. The model implies a maturation of the whole person, not only a skill upgrade.

### Modern Applications

- **Learning design patterns**: Apply patterns literally (Shu), adapt them to project context (Ha), and eventually design from first principles without needing patterns (Ri).
- **Scrum adoption**: Start by following the Scrum Guide strictly, then adapt events and roles once the team has matured.
- **Onboarding in general**: As a guide for how a mentor's stance should shift from prescribing procedures to reviewing decisions to co-creating systems.
