# Methodology of Multimodal Video Data Labeling

**Practical Session: Data Labeling**  
**Date:** December 9, 2025 
**Organizers:** Russian-Chinese Center for Artificial Intelligence in Education  
**Partners:** MGPU (Moscow City University) × CCNU (Central China Normal University)

---

## 📋 General Information

### Source Video

**Google Drive Link:**  
[https://drive.google.com/file/d/1__Lc0k--RsblFDU4bJoxNMKDIhsvwtdz/view?usp=drive_link](https://drive.google.com/file/d/1__Lc0k--RsblFDU4bJoxNMKDIhsvwtdz/view?usp=drive_link)

### Video Technical Specifications

| Parameter | Value |
|-----------|-------|
| **Codec** | h264 |
| **Resolution** | 1280×720p |
| **Bitrate** | 2842.09 kbps |
| **Frame Rate** | 25.00 FPS |

### Expert Annotation Requirements

**Number of Experts from CCNU:** 3 (three) specialists

All three experts will conduct independent annotation of the same video to ensure data reliability and consistency.

**Labeling Platform:** Label Studio

<img src="https://user-images.githubusercontent.com/12534576/192582340-4c9e4401-1fe6-4dbb-95bb-fdbba5493f61.png"/>

![GitHub](https://img.shields.io/github/license/heartexlabs/label-studio?logo=heartex) ![label-studio:build](https://github.com/HumanSignal/label-studio/workflows/label-studio:build/badge.svg) ![GitHub release](https://img.shields.io/github/v/release/heartexlabs/label-studio?include_prereleases)

[Website](https://labelstud.io/) • [Docs](https://labelstud.io/guide/) • [Join Slack Community <img src="https://app.heartex.ai/docs/images/slack-mini.png" width="18px"/>](https://slack.labelstud.io/?source=github-1)




---

## 🎯 Labeling Types

The video will be annotated across three parallel dimensions:

1. **Audio Labeling** — Pedagogical Speech Acts
2. **Video Labeling (Communicative Modalities)** — Verbal, Nonverbal, and Paraverbal Components
3. **Video Labeling (Pedagogical Actions)** — Specific Pedagogical Practices

---

## 📊 Template 1. Audio Labeling (Pedagogical Speech Acts)

### Description
Annotation is performed based on the audio track with visual reference to the video. The main task is to identify pedagogical speech acts on the timeline.

### Annotation Field Structure

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| **🎯 Target Episode** | Label | Required | Marking the target episode on the audio timeline |
| **👤 Initiator** | Single Choice | Required | Who is the initiator of the action:<br>• Teacher<br>• Student<br>• Group of students |
| **📂 Main Category** | Single Choice | Required | Always select: **Pedagogical Speech Acts** |
| **🗂️ Subcategory** | Multiple Choice | Required | Type of speech act (multiple selections allowed):<br>• **Question** — Asking a question<br>• **Command** — Giving a command/instruction<br>• **Encouragement** — Providing encouragement<br>• **Correction** — Making a correction<br>• **Remark/Utterance** — Making a remark/statement<br>• **Pause** — Pause in speech |
| **📝 Detailed Action Description** | Text Area | Required | Objective description of what happens.<br>*Example:* "The teacher walks to the board, points to the graph, and asks: 'Why is there a peak here?'" |
| **💡 Context and Pedagogical Significance** | Text Area | Required | **Three parts required:**<br>1. **Context BEFORE:** What was happening prior to this moment?<br>2. **Response AFTER:** How did the students react?<br>3. **Pedagogical meaning:** Why is this effective? Link to theory. |

### Features
- Main annotation component — **Audio** (synchronized with video)
- Video is used as visual context (visible="false")
- Audio playback speed is fixed (speed="false")

---

## 📊 Template 2. Video Labeling — Communicative Modalities

### Description
Annotation of visual communication components with temporal intervals and spatial positioning (via rectangles).

### Annotation Field Structure

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| **🎯 Target Episode** | Label | Required | Marking the target episode on video |
| **📦 Rectangle** | VideoRectangle | Required | Mandatory area selection on video (strokeWidth="2") |
| **👤 Initiator** | Single Choice | Required | Who is the initiator of the action:<br>• Teacher<br>• Student<br>• Group of students |
| **📂 Main Category** | Single Choice | Required | Always select: **Communicative Modalities** |
| **🗂️ Subcategory** | Multiple Choice | Required | Type of communicative modality (multiple selections allowed):<br>• **Verbal** — Verbal (speech)<br>• **Nonverbal** — Nonverbal (gestures, facial expressions, body position)<br>• **Paraverbal** — Paraverbal (intonation, pace, volume) |
| **📝 Detailed Action Description** | Text Area | Required | Objective description of what happens.<br>*Example:* "The teacher walks to the board, points to the graph, and asks: 'Why is there a peak here?'" |
| **💡 Context and Pedagogical Significance** | Text Area | Required | **Three parts required:**<br>1. **Context BEFORE:** What was happening prior to this moment?<br>2. **Response AFTER:** How did the students react?<br>3. **Pedagogical meaning:** Why is this effective? Link to theory. |

### Features
- Video and audio are synchronized (sync)
- Frame rate: 25.0 fps
- **MANDATORY:** Use of VideoRectangle tool for area selection

---

## 📊 Template 3. Video Labeling — Pedagogical Actions

### Description
Annotation of specific pedagogical practices and methodological techniques used in the learning process.

### Annotation Field Structure

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| **🎯 Target Episode** | Label | Required | Marking the target episode on video |
| **📦 Rectangle** | VideoRectangle | Required | Mandatory area selection on video (strokeWidth="2") |
| **👤 Initiator** | Single Choice | Required | Who is the initiator of the action:<br>• Teacher<br>• Student<br>• Group of students |
| **📂 Main Category** | Single Choice | Required | Always select: **Pedagogical Actions** |
| **🗂️ Subcategory** | Multiple Choice | Required | Type of pedagogical action (multiple selections allowed):<br>• **Asks an open-ended question** — Poses open-ended question<br>• **Asks a closed question** — Poses closed question<br>• **Provides clarification/explanation** — Gives clarification/explanation<br>• **Evaluates response (praise)** — Evaluates response (praise)<br>• **Evaluates response (constructive criticism)** — Evaluates response (constructive criticism)<br>• **Organizes group work** — Organizes group work<br>• **Uses scaffolding** — Uses scaffolding techniques<br>• **Other** — Other |
| **📝 Detailed Action Description** | Text Area | Required | Objective description of what happens.<br>*Example:* "The teacher walks to the board, points to the graph, and asks: 'Why is there a peak here?'" |
| **💡 Context and Pedagogical Significance** | Text Area | Required | **Three parts required:**<br>1. **Context BEFORE:** What was happening prior to this moment?<br>2. **Response AFTER:** How did the students react?<br>3. **Pedagogical meaning:** Why is this effective? Link to educational theory. |

### Features
- Video and audio are synchronized (sync)
- Frame rate: 25.0 fps
- **MANDATORY:** Use of VideoRectangle tool for area selection

---

## 🎓 Annotation Guidelines

### General Principles

1. **Objectivity:** Describe observable actions, not interpretations
2. **Context Completeness:** Always fill in all three parts in the "Context and Pedagogical Significance" field
3. **Consistency:** All three experts work independently to ensure inter-rater reliability
4. **Timestamp Accuracy:** Mark episodes as precisely as possible on the timeline

### Working with Multiple Choice

In "Subcategory" fields, you can select **multiple values simultaneously** if the episode contains a combination of elements. For example:
- Verbal + Nonverbal (speech accompanied by gestures)
- Question + Encouragement (question with elements of encouragement)

### Filling "Context and Pedagogical Significance" Field

This field is **crucial** for quality analysis. Always structure your response in three points:

```
1. Context BEFORE: [What was happening before this episode? What was the classroom atmosphere?]

2. Response AFTER: [How did the students react? Did their behavior change?]

3. Pedagogical meaning: [Why is the chosen strategy effective? What theory supports this?]
```

---

## 📞 Contact and Support

If you have any questions regarding the annotation methodology, please contact the project coordinators.

**We wish you productive work at the practical session on December 9!**

---

*Document prepared for Russian-Chinese Center for Artificial Intelligence in Education*  
*MGPU × CCNU, 2025*
