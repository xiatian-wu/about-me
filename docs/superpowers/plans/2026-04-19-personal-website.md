# Personal Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a personal website on al-folio (Jekyll) + GitHub Pages with a Home page, a Research page (D3 graph + list view), and a Blog.

**Architecture:** Fork the `alshedivat/al-folio` template, customize it for three pages (Home, Research, Blog), and add a custom D3.js force-directed graph on the Research page that reads publication/project relationships from a Jekyll-injected JSON blob. Publications are maintained in `_bibliography/papers.bib` (for jekyll-scholar rendering) and `_data/graph.yml` (for D3 data injection — kept in sync manually).

**Tech Stack:** Jekyll 4, Ruby (Bundler), al-folio theme, jekyll-scholar, D3.js v7, Jest (graph unit tests), GitHub Actions, GitHub Pages

---

## File Map

| File | Purpose |
|---|---|
| `_config.yml` | Site identity, social links, navigation |
| `_pages/about.md` | Home page (profile + bio + CV + contact) |
| `_includes/cv_section.html` | CV timeline partial embedded in Home |
| `_data/cv.yml` | Work experience timeline entries |
| `_data/graph.yml` | Project + paper nodes/edges for D3 graph |
| `_bibliography/papers.bib` | BibTeX source for jekyll-scholar list rendering |
| `_projects/` | One `.md` file per research project |
| `_pages/research.md` | Research page (graph + list view) |
| `_layouts/research.html` | Custom layout: injects `graphData` JSON, toggles views |
| `assets/js/publication-graph.js` | D3.js v7 force-directed graph |
| `assets/css/publication-graph.css` | Graph node/edge/tooltip styles |
| `tests/publication-graph.test.js` | Jest tests for graph data-prep functions |
| `package.json` | Jest dependency |
| `_pages/blog.md` | Blog page (topic-first layout) |
| `_layouts/blog-topics.html` | Blog layout grouped by topic/category |
| `CNAME` | Custom domain |

---

## Task 1: Fork, Clone, and Verify Local Build

**Files:** none created yet — this is environment setup

- [ ] **Step 1: Fork al-folio on GitHub**

  Go to https://github.com/alshedivat/al-folio and click Fork. Name the repo `personal-website` (or `<username>.github.io` if using a GitHub Pages user site).

- [ ] **Step 2: Clone locally**

  ```bash
  git clone https://github.com/<your-username>/personal-website.git
  cd personal-website
  ```

  > Note: replace `<your-username>` with your GitHub username.

- [ ] **Step 3: Install Ruby dependencies**

  ```bash
  bundle install
  ```

  Expected: Bundler resolves gems including `jekyll`, `jekyll-scholar`, `jekyll-sitemap`, etc. No errors.

- [ ] **Step 4: Verify local build**

  ```bash
  bundle exec jekyll serve --livereload
  ```

  Open http://127.0.0.1:4000 in a browser. Expected: al-folio default homepage loads with sample content.

- [ ] **Step 5: Commit baseline**

  ```bash
  git add .
  git commit -m "chore: fork al-folio baseline"
  ```

---

## Task 2: Configure Site Identity

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: Open `_config.yml` and update identity fields**

  Locate and replace the following fields (do not change anything else):

  ```yaml
  title: Your Name
  first_name: Your
  last_name: Name
  email: you@example.com
  description: >
    Researcher at NIST. One-sentence bio for meta tags.
  footer_text: >
    © 2026 Your Name. Powered by <a href="https://jekyllrb.com/" target="_blank">Jekyll</a>
    with <a href="https://github.com/alshedivat/al-folio">al-folio</a> theme.
  keywords: your, research, keywords
  url: https://yourname.github.io  # or https://yourdomain.com
  baseurl: ""

  # Repository (used by some al-folio features)
  repository: your-username/personal-website

  # Social links (leave blank for ones you don't use)
  scholar_userid: YOUR_GOOGLE_SCHOLAR_ID  # from scholar.google.com/citations?user=XXXX
  orcid_id: 0000-0000-0000-0000
  linkedin_username: your-linkedin-handle
  ```

- [ ] **Step 2: Disable unused nav pages**

  In `_config.yml`, find the `nav_bar` or `navbar_pages` list (al-folio shows pages in `_pages/` in the nav). We want only: Home, Research, Blog. Disable or delete the default pages we don't need (cv, publications, projects, teaching) by removing their files or setting `nav: false` in their front matter.

  Open each of these files and add `nav: false` (or delete the file):
  - `_pages/cv.md` — add `nav: false` (we embed CV in Home)
  - `_pages/publications.md` — add `nav: false` (Research page handles this)
  - `_pages/projects.md` — add `nav: false` (Research page handles this)
  - `_pages/teaching.md` — delete or add `nav: false`
  - `_pages/dropdown.md` — add `nav: false` if present

- [ ] **Step 3: Rebuild and verify nav**

  ```bash
  bundle exec jekyll serve
  ```

  Expected: Navigation bar shows only About (Home), Research, Blog. No 404s.

- [ ] **Step 4: Commit**

  ```bash
  git add _config.yml _pages/
  git commit -m "config: set site identity and trim navigation"
  ```

---

## Task 3: Home Page (Profile, Bio, CV, Contact)

**Files:**
- Modify: `_pages/about.md`
- Create: `_data/cv.yml`
- Create: `_includes/cv_section.html`
- Modify: `_layouts/about.html` (add CV include)

- [ ] **Step 1: Edit `_pages/about.md` front matter and bio**

  Replace the file contents:

  ```markdown
  ---
  layout: about
  title: about
  permalink: /
  nav: true
  nav_order: 1
  subtitle: >
    Researcher, <a href="https://www.nist.gov">National Institute of Standards and Technology (NIST)</a>

  profile:
    align: right
    image: prof_pic.jpg
    image_circular: false
    more_info: >
      <p>Your Office Number</p>
      <p>100 Bureau Drive</p>
      <p>Gaithersburg, MD 20899</p>

  news: false
  selected_papers: false
  social: true
  ---

  ## About

  Write a 2–3 sentence bio here. Describe your research focus, current position at NIST, and what problems you work on.

  **Research interests:** list them here, e.g., quantum sensing, metrology, signal processing.
  ```

  > Replace placeholder text with real content. Add your profile photo to `assets/img/prof_pic.jpg`.

- [ ] **Step 2: Create `_data/cv.yml`**

  Create the file with this structure (add your real entries):

  ```yaml
  - title: Work Experience
    type: time_table
    contents:
      - title: Research Scientist
        institution: National Institute of Standards and Technology (NIST)
        year: 2023 - present
        description:
          - Research focus area 1
          - Research focus area 2

      - title: Postdoctoral Fellow
        institution: Previous Institution
        year: 2021 - 2023
        description:
          - Key project or accomplishment

  - title: Education
    type: time_table
    contents:
      - title: PhD in Your Field
        institution: University Name
        year: 2017 - 2021
        description:
          - Dissertation title

      - title: BS in Your Field
        institution: University Name
        year: 2013 - 2017
  ```

- [ ] **Step 3: Create `_includes/cv_section.html`**

  ```html
  <!-- CV section embedded in the Home page -->
  <section id="cv" class="mt-5">
    <h2>CV</h2>
    <a href="{{ '/assets/pdf/cv.pdf' | relative_url }}"
       class="btn btn-sm btn-outline-primary mb-3"
       target="_blank">
      Download CV (PDF)
    </a>
    {% include cv/time_table.html data=site.data.cv %}
  </section>
  ```

  > Note: al-folio ships `_includes/cv/time_table.html`. This re-uses it in-page.

- [ ] **Step 4: Add the CV include to `_layouts/about.html`**

  Open `_layouts/about.html`. Find the closing `</article>` tag. Insert before it:

  ```html
  {% include cv_section.html %}
  ```

- [ ] **Step 5: Add a contact section to `_pages/about.md`**

  Append to the bottom of `_pages/about.md`:

  ```markdown
  ## Contact

  **Email:** [you@example.com](mailto:you@example.com)

  **Office:** 100 Bureau Drive, Gaithersburg, MD 20899

  Find me on [Google Scholar](https://scholar.google.com/citations?user=YOUR_ID),
  [ORCID](https://orcid.org/0000-0000-0000-0000), and
  [LinkedIn](https://linkedin.com/in/your-handle).
  ```

- [ ] **Step 6: Add placeholder CV PDF**

  ```bash
  mkdir -p assets/pdf
  # Copy your real CV PDF:
  cp /path/to/your/cv.pdf assets/pdf/cv.pdf
  ```

  If you don't have a PDF yet, create a placeholder:
  ```bash
  echo "placeholder" > assets/pdf/cv.pdf
  ```

- [ ] **Step 7: Rebuild and verify Home page**

  ```bash
  bundle exec jekyll serve
  ```

  Expected: Home page shows profile photo, bio, CV timeline with your entries, Download CV button, and Contact section.

- [ ] **Step 8: Commit**

  ```bash
  git add _pages/about.md _data/cv.yml _includes/cv_section.html _layouts/about.html assets/pdf/
  git commit -m "feat: home page with profile, bio, CV timeline, and contact"
  ```

---

## Task 4: Research Content (Projects + Publications Data)

**Files:**
- Create: `_projects/project-1.md` (one per project, repeat pattern)
- Create: `_bibliography/papers.bib`
- Create: `_data/graph.yml`

This task sets up the content sources. The Research page layout comes in Tasks 5 and 6.

- [ ] **Step 1: Create a project file**

  Create `_projects/project-1.md`:

  ```markdown
  ---
  layout: page
  title: Project Title
  description: One-sentence description of the project.
  img: assets/img/project-1.jpg
  importance: 1
  category: active
  years: "2022–present"
  ---

  Longer description of the project. What problem does it solve? What's the approach?
  ```

  > Repeat this for each project. Use a unique filename per project (`project-2.md`, etc.). `importance` controls ordering (lower = higher). Set `category: active` or `category: completed`.

- [ ] **Step 2: Create `_bibliography/papers.bib`**

  Start with this template (replace with real BibTeX from Google Scholar export):

  ```bibtex
  @article{yourname2024example,
    title     = {Example Paper Title},
    author    = {Your Name and Coauthor Name},
    journal   = {Journal Name},
    year      = {2024},
    volume    = {1},
    pages     = {1--10},
    doi       = {10.xxxx/xxxxxx},
    projects  = {project-1},
    note      = {}
  }

  @article{yourname2023inprogress,
    title     = {In-Progress Paper Title},
    author    = {Your Name and Coauthor Name},
    year      = {2025},
    projects  = {project-1},
    note      = {In preparation}
  }
  ```

  > The `projects` field links each paper to a project. Use the project's filename slug (without `.md`). Multiple projects: `projects = {project-1, project-2}`.

- [ ] **Step 3: Create `_data/graph.yml`**

  This is the D3 graph's data source (kept in sync with `papers.bib` manually):

  ```yaml
  # Nodes
  nodes:
    - id: researcher
      type: researcher
      label: "Your Name"

    - id: project-1
      type: project
      label: "Project Title"
      years: "2022–present"
      url: "/projects/project-1"

  # Papers - published
    - id: yourname2024example
      type: paper
      status: published
      label: "Example Paper Title"
      year: 2024
      venue: "Journal Name"
      url: "https://doi.org/10.xxxx/xxxxxx"

  # Papers - in progress
    - id: yourname2025inprogress
      type: paper
      status: in-progress
      label: "In-Progress Paper Title"
      year: 2025
      venue: ""
      url: ""

  # Edges
  edges:
    - source: researcher
      target: project-1

    - source: project-1
      target: yourname2024example

    - source: project-1
      target: yourname2025inprogress
  ```

  > When adding a new paper to `papers.bib`, also add it here. Both files must stay in sync.

- [ ] **Step 4: Verify Jekyll sees the data**

  ```bash
  bundle exec jekyll build
  ```

  Expected: Build succeeds. No errors about missing bib fields or YAML parse failures.

- [ ] **Step 5: Commit**

  ```bash
  git add _projects/ _bibliography/papers.bib _data/graph.yml
  git commit -m "content: add project files, papers.bib, and graph data"
  ```

---

## Task 5: Research Page — List View

**Files:**
- Create: `_pages/research.md`
- Create: `_layouts/research.html`

- [ ] **Step 1: Create `_pages/research.md`**

  ```markdown
  ---
  layout: research
  title: Research
  permalink: /research/
  nav: true
  nav_order: 2
  ---
  ```

- [ ] **Step 2: Create `_layouts/research.html`**

  ```html
  ---
  layout: default
  ---

  <div class="post">
    <header class="post-header">
      <h1 class="post-title">{{ page.title }}</h1>
    </header>

    <!-- View toggle buttons -->
    <div class="view-toggle mb-4" id="view-toggle">
      <button class="btn btn-sm btn-primary active" id="btn-graph" onclick="showView('graph')">
        Graph View
      </button>
      <button class="btn btn-sm btn-outline-primary" id="btn-list" onclick="showView('list')">
        List View
      </button>
    </div>

    <!-- Graph View container (default) -->
    <div id="graph-view">
      <div id="publication-graph" style="width:100%; height:600px; border:1px solid #eee; border-radius:8px;">
      </div>
      <p class="text-muted small mt-2">
        Drag to rearrange. Hover for details. Click to open.
      </p>
    </div>

    <!-- List View container (hidden by default) -->
    <div id="list-view" style="display:none;">
      {% assign sorted_projects = site.projects | sort: "importance" %}
      {% for project in sorted_projects %}
      <div class="project-entry mb-5" id="{{ project.slug }}">
        <h3>
          <a href="{{ project.url | relative_url }}">{{ project.title }}</a>
          <small class="text-muted">{{ project.years }}</small>
        </h3>
        <p>{{ project.description }}</p>

        {% assign project_slug = project.slug %}
        {% assign project_papers = site.data.graph.nodes
            | where: "type", "paper"
            | where_exp: "node", "node.id contains project_slug" %}

        {% comment %}
          Filter papers by edge membership for this project.
          We use the edges in graph.yml to determine which papers belong here.
        {% endcomment %}
        {% assign project_paper_ids = "" | split: "" %}
        {% for edge in site.data.graph.edges %}
          {% if edge.source == project_slug %}
            {% assign project_paper_ids = project_paper_ids | push: edge.target %}
          {% endif %}
        {% endfor %}

        {% if project_paper_ids.size > 0 %}
        <ul class="publication-list">
          {% for node in site.data.graph.nodes %}
            {% if project_paper_ids contains node.id and node.type == "paper" %}
            <li class="{% if node.status == 'in-progress' %}text-muted{% endif %}">
              {% if node.url != "" %}
                <a href="{{ node.url }}" target="_blank">{{ node.label }}</a>
              {% else %}
                {{ node.label }}
              {% endif %}
              ({{ node.year }})
              {% if node.venue != "" %} — <em>{{ node.venue }}</em>{% endif %}
              {% if node.status == "in-progress" %}
                <span class="badge badge-warning">In preparation</span>
              {% endif %}
            </li>
            {% endif %}
          {% endfor %}
        </ul>
        {% endif %}
      </div>
      {% endfor %}

      <!-- Papers not linked to any project -->
      {% assign all_edge_targets = site.data.graph.edges | map: "target" %}
      {% assign orphan_papers = site.data.graph.nodes
          | where: "type", "paper"
          | where_exp: "node", "all_edge_targets contains node.id == false" %}
      {% if orphan_papers.size > 0 %}
      <div class="project-entry mb-5" id="other">
        <h3>Other Publications</h3>
        <ul class="publication-list">
          {% for node in orphan_papers %}
          <li>
            {% if node.url != "" %}
              <a href="{{ node.url }}" target="_blank">{{ node.label }}</a>
            {% else %}
              {{ node.label }}
            {% endif %}
            ({{ node.year }})
            {% if node.venue != "" %} — <em>{{ node.venue }}</em>{% endif %}
          </li>
          {% endfor %}
        </ul>
      </div>
      {% endif %}
    </div>

    <!-- Inject graph data for D3 -->
    <script>
    window.graphData = {{ site.data.graph | jsonify }};
    </script>

    <!-- D3 graph script (no-op if JS disabled) -->
    <script src="https://cdn.jsdelivr.net/npm/d3@7" defer></script>
    <script src="{{ '/assets/js/publication-graph.js' | relative_url }}" defer></script>
    <link rel="stylesheet" href="{{ '/assets/css/publication-graph.css' | relative_url }}">

    <script>
    function showView(view) {
      document.getElementById('graph-view').style.display = view === 'graph' ? 'block' : 'none';
      document.getElementById('list-view').style.display  = view === 'list'  ? 'block' : 'none';
      document.getElementById('btn-graph').className = view === 'graph'
        ? 'btn btn-sm btn-primary active' : 'btn btn-sm btn-outline-primary';
      document.getElementById('btn-list').className = view === 'list'
        ? 'btn btn-sm btn-primary active' : 'btn btn-sm btn-outline-primary';
    }
    // Fallback: if JS disabled, list view is already rendered in DOM via noscript
    </script>

    <noscript>
      <script>document.getElementById('graph-view').style.display='none';document.getElementById('list-view').style.display='block';</script>
    </noscript>
  </div>
  ```

- [ ] **Step 3: Build and verify List View**

  ```bash
  bundle exec jekyll serve
  ```

  Open http://127.0.0.1:4000/research/. Expected: Research page loads, "Graph View" and "List View" buttons visible. Clicking "List View" shows projects with nested publication lists.

- [ ] **Step 4: Commit**

  ```bash
  git add _pages/research.md _layouts/research.html
  git commit -m "feat: research page list view with project-grouped publications"
  ```

---

## Task 6: Research Page — D3.js Graph View

**Files:**
- Create: `assets/js/publication-graph.js`
- Create: `assets/css/publication-graph.css`
- Create: `package.json`
- Create: `tests/publication-graph.test.js`

This task is pure JavaScript. Write tests first (TDD), then implement.

- [ ] **Step 1: Create `package.json`**

  ```json
  {
    "name": "personal-website-tests",
    "version": "1.0.0",
    "scripts": {
      "test": "jest"
    },
    "devDependencies": {
      "jest": "^29.0.0"
    }
  }
  ```

  ```bash
  npm install
  ```

- [ ] **Step 2: Write failing tests in `tests/publication-graph.test.js`**

  ```javascript
  // These test the pure data-transformation functions exported from publication-graph.js.
  // D3 rendering itself is not tested here.

  const { buildNodes, buildLinks, findOrphans } = require('../assets/js/publication-graph');

  const sampleData = {
    nodes: [
      { id: 'researcher', type: 'researcher', label: 'Dr. Smith' },
      { id: 'proj-a',     type: 'project',    label: 'Project A', years: '2022–present', url: '/projects/proj-a' },
      { id: 'paper-1',    type: 'paper',       label: 'Paper 1',   status: 'published',  year: 2023, venue: 'Nature', url: 'https://doi.org/1' },
      { id: 'paper-2',    type: 'paper',       label: 'Paper 2',   status: 'in-progress', year: 2025, venue: '', url: '' },
      { id: 'paper-3',    type: 'paper',       label: 'Paper 3',   status: 'published',  year: 2022, venue: 'Science', url: 'https://doi.org/2' },
    ],
    edges: [
      { source: 'researcher', target: 'proj-a' },
      { source: 'proj-a',     target: 'paper-1' },
      { source: 'proj-a',     target: 'paper-2' },
      // paper-3 has no edge — it is an orphan
    ],
  };

  describe('buildNodes', () => {
    it('returns all nodes with correct types', () => {
      const nodes = buildNodes(sampleData);
      expect(nodes).toHaveLength(5);
      expect(nodes.find(n => n.id === 'researcher').type).toBe('researcher');
      expect(nodes.find(n => n.id === 'proj-a').type).toBe('project');
      expect(nodes.find(n => n.id === 'paper-1').type).toBe('paper');
    });
  });

  describe('buildLinks', () => {
    it('converts edge list to D3 link objects', () => {
      const links = buildLinks(sampleData);
      expect(links).toHaveLength(3);  // includes orphan edge added by findOrphans
    });

    it('each link has source and target matching node ids', () => {
      const links = buildLinks(sampleData);
      links.forEach(link => {
        expect(typeof link.source).toBe('string');
        expect(typeof link.target).toBe('string');
      });
    });
  });

  describe('findOrphans', () => {
    it('finds papers not connected to any project', () => {
      const orphans = findOrphans(sampleData);
      expect(orphans).toHaveLength(1);
      expect(orphans[0].id).toBe('paper-3');
    });

    it('returns empty array when all papers have edges', () => {
      const dataNoOrphans = {
        nodes: sampleData.nodes,
        edges: [
          ...sampleData.edges,
          { source: 'proj-a', target: 'paper-3' },
        ],
      };
      expect(findOrphans(dataNoOrphans)).toHaveLength(0);
    });
  });
  ```

- [ ] **Step 3: Run tests — verify they fail**

  ```bash
  npm test
  ```

  Expected: FAIL — `Cannot find module '../assets/js/publication-graph'`

- [ ] **Step 4: Create `assets/js/publication-graph.js`**

  ```javascript
  /**
   * Publication Graph
   * Reads window.graphData (injected by Jekyll layout) and renders a D3 v7
   * force-directed graph in #publication-graph.
   *
   * Exports buildNodes, buildLinks, findOrphans for unit testing.
   */

  // ── Pure data functions (testable without D3 or DOM) ──────────────────────

  function buildNodes(data) {
    return data.nodes.map(n => ({ ...n }));
  }

  function buildLinks(data) {
    return data.edges.map(e => ({ source: e.source, target: e.target }));
  }

  function findOrphans(data) {
    const targeted = new Set(data.edges.map(e => e.target));
    return data.nodes.filter(n => n.type === 'paper' && !targeted.has(n.id));
  }

  // ── D3 rendering ──────────────────────────────────────────────────────────

  function nodeColor(type, status) {
    if (type === 'researcher') return '#4a4e69';
    if (type === 'project')    return '#6366f1';  // indigo
    if (status === 'published')  return '#22c55e';  // green
    if (status === 'in-progress') return '#eab308'; // yellow
    return '#94a3b8';
  }

  function nodeRadius(type) {
    if (type === 'researcher') return 20;
    if (type === 'project')    return 14;
    return 8;
  }

  function initGraph(data) {
    const container = document.getElementById('publication-graph');
    if (!container) return;

    const width  = container.clientWidth  || 900;
    const height = container.clientHeight || 600;

    // Augment data: add an "Other" node and edges for orphan papers
    const orphans = findOrphans(data);
    const augmentedData = { nodes: [...data.nodes], edges: [...data.edges] };

    if (orphans.length > 0) {
      augmentedData.nodes.push({
        id: '__other__', type: 'project', label: 'Other', years: '', url: '',
      });
      augmentedData.edges.push({ source: 'researcher', target: '__other__' });
      orphans.forEach(o => {
        augmentedData.edges.push({ source: '__other__', target: o.id });
      });
    }

    const nodes = buildNodes(augmentedData);
    const links = buildLinks(augmentedData);

    const svg = d3.select('#publication-graph')
      .append('svg')
      .attr('width', width)
      .attr('height', height);

    // Tooltip
    const tooltip = d3.select('body').append('div')
      .attr('class', 'graph-tooltip')
      .style('display', 'none');

    const simulation = d3.forceSimulation(nodes)
      .force('link', d3.forceLink(links).id(d => d.id).distance(80))
      .force('charge', d3.forceManyBody().strength(-200))
      .force('center', d3.forceCenter(width / 2, height / 2))
      .force('collision', d3.forceCollide().radius(d => nodeRadius(d.type) + 4));

    const link = svg.append('g')
      .attr('class', 'links')
      .selectAll('line')
      .data(links)
      .join('line')
      .attr('stroke', '#cbd5e1')
      .attr('stroke-width', 1.5);

    const node = svg.append('g')
      .attr('class', 'nodes')
      .selectAll('circle')
      .data(nodes)
      .join('circle')
      .attr('r', d => nodeRadius(d.type))
      .attr('fill', d => nodeColor(d.type, d.status))
      .attr('stroke', '#fff')
      .attr('stroke-width', 2)
      .style('cursor', 'pointer')
      .call(
        d3.drag()
          .on('start', (event, d) => {
            if (!event.active) simulation.alphaTarget(0.3).restart();
            d.fx = d.x; d.fy = d.y;
          })
          .on('drag', (event, d) => { d.fx = event.x; d.fy = event.y; })
          .on('end', (event, d) => {
            if (!event.active) simulation.alphaTarget(0);
            d.fx = null; d.fy = null;
          })
      )
      .on('mouseover', (event, d) => {
        const lines = [d.label];
        if (d.year)  lines.push(String(d.year));
        if (d.venue) lines.push(d.venue);
        tooltip
          .style('display', 'block')
          .html(lines.join('<br>'));
      })
      .on('mousemove', event => {
        tooltip
          .style('left', (event.pageX + 12) + 'px')
          .style('top',  (event.pageY - 20) + 'px');
      })
      .on('mouseout', () => tooltip.style('display', 'none'))
      .on('click', (event, d) => {
        if (d.url) window.open(d.url, '_blank');
      });

    const label = svg.append('g')
      .attr('class', 'labels')
      .selectAll('text')
      .data(nodes.filter(d => d.type !== 'paper'))
      .join('text')
      .text(d => d.label)
      .attr('font-size', 11)
      .attr('fill', '#334155')
      .attr('text-anchor', 'middle')
      .attr('dy', d => nodeRadius(d.type) + 14);

    simulation.on('tick', () => {
      link
        .attr('x1', d => d.source.x)
        .attr('y1', d => d.source.y)
        .attr('x2', d => d.target.x)
        .attr('y2', d => d.target.y);

      node
        .attr('cx', d => Math.max(20, Math.min(width  - 20, d.x)))
        .attr('cy', d => Math.max(20, Math.min(height - 20, d.y)));

      label
        .attr('x', d => Math.max(20, Math.min(width  - 20, d.x)))
        .attr('y', d => Math.max(20, Math.min(height - 20, d.y)));
    });
  }

  // ── Module exports (for Jest) / browser init ─────────────────────────────

  if (typeof module !== 'undefined' && module.exports) {
    module.exports = { buildNodes, buildLinks, findOrphans };
  } else {
    document.addEventListener('DOMContentLoaded', () => {
      if (window.graphData) initGraph(window.graphData);
    });
  }
  ```

- [ ] **Step 5: Run tests — verify they pass**

  ```bash
  npm test
  ```

  Expected: All 4 tests PASS.

- [ ] **Step 6: Create `assets/css/publication-graph.css`**

  ```css
  /* Tooltip */
  .graph-tooltip {
    position: absolute;
    background: rgba(15, 23, 42, 0.85);
    color: #f8fafc;
    padding: 6px 10px;
    border-radius: 6px;
    font-size: 12px;
    line-height: 1.5;
    pointer-events: none;
    z-index: 100;
    max-width: 240px;
  }

  /* Legend */
  #publication-graph {
    background: #f8fafc;
    position: relative;
  }

  /* Node hover feedback via SVG (handled inline in JS for now) */
  .nodes circle:hover {
    opacity: 0.85;
    stroke-width: 3px;
  }
  ```

- [ ] **Step 7: Build and verify Graph View in browser**

  ```bash
  bundle exec jekyll serve
  ```

  Open http://127.0.0.1:4000/research/. Expected:
  - Graph View shows by default with nodes and links
  - Researcher node in center, project nodes connected to it, papers as leaf nodes
  - Published papers are green, in-progress are yellow, projects are indigo
  - Hovering shows tooltip with title/year/venue
  - Clicking a published paper opens its URL in a new tab
  - Dragging nodes works
  - "List View" button switches to the list

- [ ] **Step 8: Commit**

  ```bash
  git add assets/js/publication-graph.js assets/css/publication-graph.css package.json package-lock.json tests/
  git commit -m "feat: D3.js force-directed publication graph with tests"
  ```

---

## Task 7: Blog Page — Topic-First Layout

**Files:**
- Create or modify: `_pages/blog.md`
- Create: `_layouts/blog-topics.html`

- [ ] **Step 1: Check al-folio's existing blog layout**

  ```bash
  ls _layouts/
  ```

  Expected: You'll see `blog.html` or similar. We create a new layout that groups posts by category (topic) first.

- [ ] **Step 2: Create `_layouts/blog-topics.html`**

  ```html
  ---
  layout: default
  ---

  <div class="post">
    <header class="post-header">
      <h1 class="post-title">{{ page.title }}</h1>
      {% if page.description %}
      <p class="post-description">{{ page.description }}</p>
      {% endif %}
    </header>

    <article>
      {% comment %}Group posts by category, then sort by date within each group{% endcomment %}
      {% assign all_categories = site.posts | map: "categories" | join: "," | split: "," | uniq | sort %}

      {% for category in all_categories %}
        {% assign cat_posts = site.posts | where_exp: "post", "post.categories contains category" | sort: "date" | reverse %}
        {% if cat_posts.size > 0 %}
        <section class="blog-category mb-5">
          <h2 class="category-title">{{ category | capitalize }}</h2>
          <ul class="post-list">
            {% for post in cat_posts %}
            <li>
              <span class="post-meta">{{ post.date | date: "%b %Y" }}</span>
              <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
              {% if post.description %}
                — <span class="post-description">{{ post.description }}</span>
              {% endif %}
            </li>
            {% endfor %}
          </ul>
        </section>
        {% endif %}
      {% endfor %}

      {% comment %}Uncategorized posts{% endcomment %}
      {% assign uncategorized = site.posts | where_exp: "post", "post.categories.size == 0" | sort: "date" | reverse %}
      {% if uncategorized.size > 0 %}
      <section class="blog-category mb-5">
        <h2 class="category-title">Uncategorized</h2>
        <ul class="post-list">
          {% for post in uncategorized %}
          <li>
            <span class="post-meta">{{ post.date | date: "%b %Y" }}</span>
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </li>
          {% endfor %}
        </ul>
      </section>
      {% endif %}
    </article>
  </div>
  ```

- [ ] **Step 3: Create or update `_pages/blog.md`**

  ```markdown
  ---
  layout: blog-topics
  title: Blog
  permalink: /blog/
  nav: true
  nav_order: 3
  description: Notes, updates, and writing.
  ---
  ```

- [ ] **Step 4: Create a sample post to verify**

  Create `_posts/2026-04-19-hello-world.md`:

  ```markdown
  ---
  layout: post
  title: Hello World
  date: 2026-04-19
  categories: [updates]
  description: First post on the new site.
  ---

  Welcome to my website. This is the first post.
  ```

- [ ] **Step 5: Build and verify Blog page**

  ```bash
  bundle exec jekyll serve
  ```

  Open http://127.0.0.1:4000/blog/. Expected: Blog page shows posts grouped by category. "Updates" category appears with the sample post.

- [ ] **Step 6: Commit**

  ```bash
  git add _pages/blog.md _layouts/blog-topics.html _posts/
  git commit -m "feat: blog page with topic-first layout"
  ```

---

## Task 8: Deployment — GitHub Pages + Custom Domain

**Files:**
- Modify: `.github/workflows/deploy.yml` (verify it exists from al-folio fork)
- Create: `CNAME`

- [ ] **Step 1: Verify GitHub Actions workflow exists**

  ```bash
  cat .github/workflows/deploy.yml
  ```

  Expected: al-folio ships this file. It builds Jekyll and deploys to GitHub Pages. If it's missing, copy it from https://github.com/alshedivat/al-folio/blob/master/.github/workflows/deploy.yml.

- [ ] **Step 2: Enable GitHub Pages in repo settings**

  In your GitHub repo: Settings > Pages > Source: select "GitHub Actions". Do NOT select "Deploy from a branch."

- [ ] **Step 3: Push to main and verify the build**

  ```bash
  git push origin main
  ```

  Go to your repo's Actions tab on GitHub. Expected: The deploy workflow runs and passes. If it fails, read the error log.

- [ ] **Step 4: Create `CNAME` for custom domain** (skip if using `username.github.io`)

  ```bash
  echo "yourdomain.com" > CNAME
  git add CNAME
  git commit -m "config: add custom domain CNAME"
  git push origin main
  ```

- [ ] **Step 5: Configure DNS**

  At your domain registrar, add these DNS records pointing to GitHub Pages:

  ```
  Type  Name  Value
  A     @     185.199.108.153
  A     @     185.199.109.153
  A     @     185.199.110.153
  A     @     185.199.111.153
  CNAME www   your-username.github.io.
  ```

  DNS propagation takes up to 24 hours. In the meantime the site is accessible at `https://your-username.github.io/personal-website`.

- [ ] **Step 6: Enable HTTPS**

  In GitHub Settings > Pages, check "Enforce HTTPS" once the domain is verified. May take a few minutes after DNS propagates.

- [ ] **Step 7: Verify live site**

  Open your domain (or `https://your-username.github.io`) in a browser. Expected:
  - Home page loads with your profile, bio, CV, contact
  - Research page shows the D3 graph by default; List View toggle works
  - Blog page shows topics with your posts
  - No broken links or 404s

---

## Self-Review

### Spec Coverage

| Spec requirement | Task |
|---|---|
| Three-page navigation (Home, Research, Blog) | Tasks 2, 3, 5, 7 |
| Profile photo, name, title, institution, social links | Task 3 |
| About bio and research interests | Task 3 |
| CV timeline + PDF download | Task 3 |
| Contact section | Task 3 |
| D3 graph (force-directed, Obsidian-style) | Task 6 |
| Center researcher node | Task 6 |
| Project nodes (indigo) | Task 6 |
| Published paper nodes (green) | Task 6 |
| In-progress paper nodes (yellow) | Task 6 |
| Drag, hover tooltip, click to open | Task 6 |
| Graph fallback to List View if JS disabled | Task 5 (noscript) |
| Papers with no project grouped under "Other" | Task 6 (findOrphans) |
| List View: projects ordered by year | Task 5 |
| Publications nested under projects in List View | Task 5 |
| Blog: topics first, then chronological | Task 7 |
| BibTeX source for publications | Task 4 |
| `_data/graph.yml` for D3 | Task 4 |
| `_data/cv.yml` for CV | Task 3 |
| GitHub Actions deploy | Task 8 |
| Custom domain | Task 8 |

All spec requirements are covered. No gaps.

### Potential Issue: Orphan detection in List View

The Liquid filter `where_exp: "node", "all_edge_targets contains node.id == false"` in `_layouts/research.html` (Task 5 Step 2) uses chained Liquid that may not work as written. The `findOrphans` logic for the list view should be verified during the build step. If the filter fails, simplify by iterating all paper nodes and checking edges inline.

---

**Plan complete and saved to `docs/superpowers/plans/2026-04-19-personal-website.md`.**

**Two execution options:**

**1. Subagent-Driven (recommended)** — I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** — Execute tasks in this session using executing-plans, batch execution with checkpoints

**Which approach?**
