# Quality Checklist

Verification criteria used by the analyst and reporter agents. Adapted per research type.

## Universal Checks (All Types)

- [ ] Every claim has a source attribution
- [ ] Confidence level assigned to each finding ([HIGH/MEDIUM/LOW])
- [ ] No contradictions left unaddressed — conflicts are flagged explicitly
- [ ] Research question is directly answered in the summary
- [ ] Sources are diverse (not all from the same domain/site)
- [ ] Data points include units and time periods
- [ ] No hallucinated URLs or citations

## Investment Type

- [ ] Financial metrics sourced from credible data (annual reports, press releases, databases)
- [ ] Market size estimates have methodology note (bottom-up, top-down, source)
- [ ] Competitive landscape covers at least 3 relevant players
- [ ] Risk factors are specific, not generic boilerplate
- [ ] PE fit assessment uses concrete evidence, not assumptions
- [ ] Comparable transactions are relevant (same sector, similar size, recent)
- [ ] European number formatting used (comma decimals, space/dot thousands)

## Ideation Type

- [ ] Trend claims backed by data or multiple sources
- [ ] Competitive landscape distinguishes direct vs indirect competitors
- [ ] Feasibility assessment considers resources, skills, and timeline
- [ ] Market size estimate exists (even rough order of magnitude)
- [ ] Next steps are concrete and actionable

## General Type

- [ ] Multiple perspectives represented (not one-sided)
- [ ] Technical terms explained or defined on first use
- [ ] Logical flow from findings to analysis to conclusions
- [ ] Further reading section included for deep topics
- [ ] Scope clearly defined — what's covered and what's not

## Personal Type (Shallow)

- [ ] Answer is direct and concise
- [ ] At least one credible source link provided
- [ ] No unnecessary elaboration

## Source-Specific Checks

### Web Sources
- [ ] Recency: are sources current enough for the topic?
- [ ] Authority: government, academic, or industry sources preferred over blogs
- [ ] No paywalled content cited without noting access limitations

### YouTube/Video
- [ ] Transcript excerpts include video title and approximate timestamp
- [ ] Speaker credibility noted (who is this person?)
- [ ] Video recency checked — old videos may have outdated information

### NotebookLM
- [ ] Citations from NotebookLM are marked as "from uploaded documents"
- [ ] Follow-up questions were asked until information was complete
- [ ] Notebook name/topic identified for user reference

### Excel/Supabase/Files
- [ ] Data freshness noted (when was the file last updated?)
- [ ] Numbers cross-checked against other sources where possible
- [ ] File path or table name included for traceability
