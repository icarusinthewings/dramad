# 🎭 DramaD Specification (v0.1 — Alpha)

A semantic markup language for dramatic works, libretti, and theatrical scripts.  
DramaD separates **dramatic meaning**, **entities**, **timing**, and **staging** from **production annotations**.

---

## 1. Philosophy

- Writer writes *poetry and meaning*
- System understands *structure and identity*
- Directors, SMs, designers annotate via `.da` overlays
- Rendering is platform‑agnostic
- Dramaturgy & staging worlds separated

**Script is truth. Production is overlay.**

---

## 2. File Types

| File | Meaning |
|---|---|
`.dd` | Core DramaD script  
`.da` | Annotation layer (director/SM/tech/academic)

Multiple `.da` files may annotate the same `.dd`.

---

## 3. Document Skeleton

```
<d>
  <meta> … </meta>
  <entities> … </entities>
  <!-- acts / chapters / scenes / sections -->
</d>
```

### 3.1 `<meta>`

Allowed:
- `<title>`
- `<author role="">name</author>`
- `<an>` general notes

---

## 4. Entities

### 4.1 Location

Immediately after `<meta>` in canonical form:

```
<entities>
  ...
</entities>
```

### 4.2 Entity Tags

| Tag | Meaning |
|---|---|
`<char>` | Character  
`<prop>` | Prop  
`<loc>` | Narrative world location  
`<space>` | Physical stage area  
`<grp>` | Group / chorus / ensemble  
`<entity>` | Generic entity class  

### 4.3 Rules

- `id=` required
- IDs = alphanumeric, case‑insensitive
- Canonical uppercase internal
- Must be unique after normalization
- Display name = element text, unless `display=` set
- Unlimited custom attributes allowed
- `<alias>` defines **additional IDs**

Example:

```xml
<char id="ICR">
  Icarus
  <alias>BOY</alias>
</char>
```

Aliases = extra handles, **not** alternate names.

---

## 5. Reserved Stage Tokens

These are **not entities** and may not be used as IDs:

```
DSR DSL DC
CR  C  CL
USR USL UC
```

Universal stage grid constants.

---

## 6. Scene Structure

| Tag | Notes |
|---|---|
`<act>` | Optional top level  
`<chapter>` | Optional  
`<scene>` | Scene (cannot contain scenes)  
`<sec>` | Section (may nest sections)  

Scenes/sections contain lines, silence, directions, subsections.

---

## 7. Lines & Text

### 7.1 Spoken & sung

| Tag | Canonical |
|---|---|
`<ln>` | spoken line  
`<lyr>` | sugar for `<ln type="lyric">`  

Attributes:

| Attr | Meaning |
|---|---|
`by` | Character(s)  
`type` | `"spoken"` default, `"lyric"`  
`e` | Expression text (Markdown allowed)  
`id` | Optional; auto‑assigned if absent  

Example:

```xml
<ln by="ICR" e="soft, *afraid*">Father?</ln>
<lyr by="ICR">I will rise…</lyr>
```

---

## 8. Silence & Timing

### 8.1 Silent Line Types

All canonicalize to `<ln>` with type metadata.

| Author | Canonical |
|---|---|
`<silence/>` | `<ln type="silence"/>`  
`<pause/>` | `<ln type="pause"/>`  
`<beat/>` | `<ln type="beat"/>`  
`<breath/>` | `<ln type="pause" length="breath"/>`  

### 8.2 Timing Attribute

`length=` accepts:

- Formal: `2s`, `0.5s`, `500ms`
- Poetic: `breath`, `moment`, `eternity`, etc.

---

## 9. Inline Timing DSL

**Splits lines** into separate AST nodes.

| Syntax | Meaning |
|---|---|
`::pause` | pause  
`::pause(2s)` | timed pause  
`::beat` | beat  
`::silence(3s)` | silence  
`::breath` | breath mark  

Example:

```
I will ::breath rise ::beat again
```

Becomes:

```xml
<ln>I will </ln>
<ln type="pause" length="breath"/>
<ln>rise </ln>
<ln type="beat"/>
<ln>again</ln>
```

---

## 10. Entity Mentions in Text

Use `@ID` inside text or directions:

```
@ICR @WINGS @SKY @PLATFORM
```

- Resolves to entity display name
- Unknown → warning

---

## 11. Directions

### 11.1 General

```
<dir>@ICR crosses to @PLATFORM</dir>
```

### 11.2 Standard entrance/exit forms

Mostly used in `.da` but allowed in `.dd`:

```
<enter who="ICR CHORUS" via="USR" with="WINGS"/>
<exit who="DLS"/>
<enterExit who="ICR DLS"/>
```

Future: `<move>` via `.da`.

---

## 12. Cues

`<cue>` = **empty line event**, not entity.

| Author | Canonical |
|---|---|
`<cue/>` | `<ln type="cue" id="SCENE.C#"/>`  
`<cue id="M1"/>` | `<ln type="cue" id="M1"/>`  

Auto‑ID if missing.

Used for:
- music cues
- lighting
- projections
- automation
- XR triggers

---

## 13. Line Addressing

- Authors may omit IDs
- Canonical form assigns stable IDs: `SECTION.L#`
- Inline timing splits count as separate lines
- `<cue/>` auto‑IDs `SECTION.C#`

---

## 14. Annotation Layers (`.da`)

Interpretation, not text.

Used for:
- blocking
- cue mapping
- immersion routes
- academic commentary

Author’s `.dd` stays untouched.

---

## 15. Markdown Rules

- Allowed only inside **text content** & `e=""`
- **Not** for structure
- Renderer controls formatting

---

## 16. Summary Table

| Domain | Mechanism |
|---|---|
Characters & props | `<char>`, `<prop>`, `<alias>`  
Story space | `<loc>`  
Stage space | `<space>`  
Groups | `<grp>` (multi‑membership allowed)  
Lines | `<ln>`, `<lyr>`  
Silence | `<silence>`, `<pause>`, `<beat>`, `<breath>`  
Inline timing | `::pause`, `::beat`, etc.  
Cues | `<cue>`  
Direction | `<dir>`, `<enter>`, `<exit>`  
Sections | `<act>`, `<chapter>`, `<scene>`, `<sec>`  
Annotation | `.da` overlays  

---

# End of DramaD Spec v0.1 (Alpha)

This document represents the current canonical logic.

