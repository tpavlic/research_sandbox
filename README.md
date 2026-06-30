# Research Sandbox

Work-in-progress interactive excursions built by
[Theodore P. Pavlic](https://search.asu.edu/profile/1995237)
to support ongoing research projects, many of them developed together with graduate
students while working toward a paper or testing a hypothesis in the field.

**Live site:** <https://tpavlic.github.io/research_sandbox/>

Each excursion is a self-contained HTML page that works standalone or can be embedded
in course management systems (e.g., Canvas LMS) as an iframe. These are experimental
demonstrations and explorations rather than finished tools, built to communicate ideas of
my own and to give students, collaborators, and myself a place to experiment with them. For
the more polished, course- and topic-oriented explainers, see the companion
[Topic Visualizers](https://github.com/tpavlic/topic_visualizers) repository.

Sharing these openly is **not** a release of the underlying ideas into the public domain. The
MIT License covers the page code, not the research ideas it illustrates. I retain authorship of
those ideas and ask that they not be reused without attribution. If you intend to build on any
idea here that has not yet been published, please reach out to me first as a professional
courtesy. These excursions do not disclose any student's unpublished work, though they may show
how much of that later work was grounded in ideas I had put forward here.

Each excursion began life as its own standalone repository; those histories are preserved
and merged into this one, so the per-excursion commit history is still visible here (see
[Provenance](#provenance) below).

---

## Contents

On the live site, each excursion is tagged with a status pill, and the legend in the page's
intro box doubles as a filter (click a pill to show only that status): **WIP** (work in progress),
**Published** (completed and carried through to publication; the pill links to the paper's DOI), or
**Archived** (set aside and no longer active, never published). All four excursions below are
currently work in progress.

### Collective Behavior

- [`ytree_detrend/`](ytree_detrend/) – Detrend and spectrally analyze trajectories of collective-transport groups (weaver ants) moving loads vertically up trees, including forked trees, by projecting motion onto the tree.
- [`rsf_explorer/`](rsf_explorer/) – Test field hypotheses about how distance interacts with other nest properties in the navigational choices of collective-transport groups and individual ants in polydomous colonies, framed through a resource selection function (RSF) for subnest destination choice.

### Multi-Robot Systems

- [`v2v_swarm_explorer/`](v2v_swarm_explorer/) – Explore strategies for battery-safe vehicle-to-vehicle (V2V) energy sharing that extend the endurance and capabilities of multi-robot teams, with an exportable related-work review.

### Reinforcement Learning and Decision Models

- [`social-cue-ql/`](social-cue-ql/) – Explore a connection between conventional reinforcement learning (Q-learning) and a psychological social-cue assay as a candidate model for the phenomena.

---

## Provenance

Each excursion was originally maintained as a separate Git repository (hosted as a GitLab/GitHub
Gist). Those repositories were rewritten so that each one's single HTML file lives in its own
subdirectory, then merged into this repository with `--allow-unrelated-histories`. The result is
a single trunk whose history fans back into four independent roots, one per excursion, with every
original commit, author, and date preserved. To inspect a single excursion's history:

```bash
git log -- rsf_explorer/
```

To see the four histories converging into the trunk:

```bash
git log --graph --oneline
```

---

## Adding a new excursion

Each excursion lives in its own subdirectory:

```bash
my_excursion/
  my_excursion.html          # self-contained page
  my_excursion-preview.png   # preview image for OG/Twitter cards (optional, pending)
```

See [`CLAUDE.md`](CLAUDE.md) for the full per-page checklist (head metadata, Open Graph and
Twitter cards, back-link footer, status pill, and index/README entries). The conventions mirror
the companion [Topic Visualizers](https://github.com/tpavlic/topic_visualizers) repository.

---

## See also

Other projects by the same author that visitors here may find complementary:

- [Topic Visualizers](https://tpavlic.github.io/topic_visualizers/) – more polished, self-contained interactive explainers covering topics in science, mathematics, statistics, and engineering.
- [Bio-Inspired AI & Optimization – Course Visualizations](https://tpavlic.github.io/asu-bioinspired-ai-and-optimization/) – supplemental interactive explorers for *IEE/CSE 598: Bio-Inspired AI and Optimization* at Arizona State University.
- [Notes, Documents, & Guides](https://github.com/tpavlic/docs-and-guides) – Markdown-formatted notes and instructional guides on a variety of fundamental and applied science and engineering topics.
- [Lectures and short video tutorials](https://www.youtube.com/@TedPavlic) on YouTube, including the [Office Hours](https://www.youtube.com/playlist?list=PLXBbGVSkQJqEFKCGlTbzBnvf96DRJ6_gi) playlist.

## License

Released under the [MIT License](LICENSE).
Copyright &copy; 2026 Theodore P. Pavlic.
