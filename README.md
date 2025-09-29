# Neo4j SEO Boost Demo

Welcome to the Neo4j SEO Boost Demo! This guide shows how Neo4j, a powerful graph database, can help improve your website’s search engine optimization (SEO) by creating smarter internal links. By modeling your website as a graph, you can identify key pages, find related content, and add links that boost your site’s visibility in search results—all without changing your website’s code.

This demo uses Neo4j’s Graph Data Science (GDS) library to analyze page importance and suggest natural, relevant links to enhance pages like product pages. We’ll walk you through a fictional bookstore website, but you can easily adapt this to your own site by using your pages’ URLs, titles, and keywords.

## Why Use Neo4j for SEO?
- **Boost Key Pages**: Direct more search engine attention to your most important pages.
- **Save Time**: Automatically find the best places to add links based on content similarity.
- **Stay Natural**: Create links that feel organic and align with search engine best practices.

## What You Need
To try this demo, you’ll need:
- A Neo4j instance (like Neo4j Desktop, Aura, or Sandbox) with the Graph Data Science (GDS) library installed.
- The APOC library for keyword processing (optional but helpful).
- The Neo4j Browser to run the provided Cypher queries.

## How It Works
This demo has six steps to model your website, analyze its structure, and optimize links for SEO. Each step includes Cypher code that you can copy and paste into the Neo4j Browser. Run them in order to see the results.

### Step 1: Add Your Website’s Pages
First, we create a graph of your website’s pages. Each page is stored as a node with details like its title, URL, keywords (to find related pages), and how many clicks it is from the homepage. Pages are labeled by type, like Homepage or Product, to make them easier to work with.

**Cypher Code**:
```cypher
// Create Nodes with :Page and Specific Page Type Labels
CREATE (p1:Page:Homepage {node_id: 1, title: 'Bookstore Home', url: '/', keywords: 'bookstore, books, reading', crawl_depth: 0})
CREATE (p2:Page:Category {node_id: 2, title: 'Fiction', url: '/fiction', keywords: 'fiction, novels', crawl_depth: 1})
CREATE (p3:Page:Category {node_id: 3, title: 'Non-Fiction', url: '/non-fiction', keywords: 'non-fiction, biographies', crawl_depth: 1})
CREATE (p4:Page:Category {node_id: 4, title: 'Children’s Books', url: '/childrens', keywords: 'children’s books, kids', crawl_depth: 1})
CREATE (p5:Page:Subcategory {node_id: 5, title: 'Mystery', url: '/fiction/mystery', keywords: 'mystery, thrillers', crawl_depth: 2})
CREATE (p6:Page:Subcategory {node_id: 6, title: 'Science Fiction', url: '/fiction/scifi', keywords: 'science fiction, sci-fi', crawl_depth: 2})
CREATE (p7:Page:Subcategory {node_id: 7, title: 'Biographies', url: '/non-fiction/biographies', keywords: 'biographies, memoirs', crawl_depth: 2})
CREATE (p8:Page:Subcategory {node_id: 8, title: 'History', url: '/non-fiction/history', keywords: 'history, historical books', crawl_depth: 2})
CREATE (p9:Page:Subcategory {node_id: 9, title: 'Picture Books', url: '/childrens/picture-books', keywords: 'picture books, kids', crawl_depth: 2})
CREATE (p10:Page:Subcategory {node_id: 10, title: 'Young Adult', url: '/childrens/young-adult', keywords: 'young adult, YA books', crawl_depth: 2})
CREATE (p11:Page:Book {node_id: 11, title: 'The Silent Clue', url: '/fiction/mystery/silent-clue', keywords: 'mystery, thriller, bestseller', crawl_depth: 3})
CREATE (p12:Page:Book {node_id: 12, title: 'Stars of Tomorrow', url: '/fiction/scifi/stars-tomorrow', keywords: 'sci-fi, space adventure', crawl_depth: 3})
CREATE (p13:Page:Book {node_id: 13, title: 'Life of a Legend', url: '/non-fiction/biographies/legend', keywords: 'biography, historical figure', crawl_depth: 3})
CREATE (p14:Page:Book {node_id: 14, title: 'Ancient Wars', url: '/non-fiction/history/ancient', keywords: 'history, ancient wars', crawl_depth: 3})
CREATE (p15:Page:Book {node_id: 15, title: 'The Magic Tree', url: '/childrens/picture-books/magic', keywords: 'picture book, kids, fantasy', crawl_depth: 3})
CREATE (p16:Page:Book {node_id: 16, title: 'Rebel Teens', url: '/childrens/young-adult/rebel', keywords: 'young adult, coming-of-age', crawl_depth: 3})
CREATE (p17:Page:Book {node_id: 17, title: 'Mystery of the Lost City', url: '/fiction/mystery/lost-city', keywords: 'mystery, adventure, bestseller', crawl_depth: 3})
CREATE (p18:Page:Book {node_id: 18, title: 'Galactic Quest', url: '/fiction/scifi/galactic-quest', keywords: 'sci-fi, space, adventure', crawl_depth: 3})
CREATE (p19:Page:Book {node_id: 19, title: 'My Life in Words', url: '/non-fiction/biographies/mylife', keywords: 'biography, memoir', crawl_depth: 3})
CREATE (p20:Page:Book {node_id: 20, title: 'The Brave Knight', url: '/childrens/picture-books/knight', keywords: 'picture book, kids, adventure', crawl_depth: 3})
CREATE (p21:Page:Blog {node_id: 21, title: 'Top 10 Mystery Novels', url: '/blog/top-10-mystery', keywords: 'mystery novels, book recommendations', crawl_depth: 2})
CREATE (p22:Page:Blog {node_id: 22, title: 'Best Kids’ Books for Summer', url: '/blog/kids-summer', keywords: 'kids books, summer reading', crawl_depth: 2})
CREATE (p23:Page:Author {node_id: 23, title: 'Jane Smith', url: '/authors/jane-smith', keywords: 'author, mystery, biography', crawl_depth: 2})
CREATE (p24:Page:Author {node_id: 24, title: 'Tom Brown', url: '/authors/tom-brown', keywords: 'author, sci-fi, kids', crawl_depth: 2})
```

### Step 2: Map Existing Links
Next, we add the links between your pages, like navigation menus or content links. These are stored as relationships in Neo4j, with weights to show their importance. This step builds the structure of your website’s graph.

**Cypher Code**:
```cypher
```cypher
// Create Relationships (Internal Links)
CREATE (p1)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.5}]->(p2) // Homepage -> Fiction
CREATE (p1)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.5}]->(p3) // Homepage -> Non-Fiction
CREATE (p1)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.5}]->(p4) // Homepage -> Children’s
CREATE (p2)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p5) // Fiction -> Mystery
CREATE (p2)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p6) // Fiction -> Sci-Fi
CREATE (p3)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p7) // Non-Fiction -> Biographies
CREATE (p3)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p8) // Non-Fiction -> History
CREATE (p4)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p9) // Children’s -> Picture Books
CREATE (p4)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.7}]->(p10) // Children’s -> Young Adult
CREATE (p5)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p11) // Mystery -> The Silent Clue
CREATE (p5)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p17) // Mystery -> Mystery of the Lost City
CREATE (p6)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p12) // Sci-Fi -> Stars of Tomorrow
CREATE (p6)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p18) // Sci-Fi -> Galactic Quest
CREATE (p7)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p13) // Biographies -> Life of a Legend
CREATE (p7)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p19) // Biographies -> My Life in Words
CREATE (p8)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p14) // History -> Ancient Wars
CREATE (p9)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p15) // Picture Books -> The Magic Tree
CREATE (p9)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p20) // Picture Books -> The Brave Knight
CREATE (p10)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p16) // Young Adult -> Rebel Teens
CREATE (p21)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p11) // Blog: Top 10 Mystery -> Silent Clue
CREATE (p21)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p17) // Blog: Top 10 Mystery -> Lost City
CREATE (p22)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p15) // Blog: Kids’ Summer -> Magic Tree
CREATE (p22)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 1.0}]->(p20) // Blog: Kids’ Summer -> Brave Knight
CREATE (p23)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p11) // Author: Jane Smith -> Silent Clue
CREATE (p23)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p17) // Author: Jane Smith -> Lost City
CREATE (p23)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p19) // Author: Jane Smith -> My Life in Words
CREATE (p24)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p12) // Author: Tom Brown -> Stars of Tomorrow
CREATE (p24)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p18) // Author: Tom Brown -> Galactic Quest
CREATE (p24)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.8}]->(p20) // Author: Tom Brown -> Brave Knight
CREATE (p11)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.6}]->(p5) // Silent Clue -> Mystery (backlink)
CREATE (p17)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.6}]->(p5) // Lost City -> Mystery (backlink)
CREATE (p17)-[:LINKS_TO {type: 'CONTENT_LINK', weight: 0.4}]->(p10) // Lost City -> Young Adult (secondary category)
CREATE (p15)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.5}]->(p1) // Magic Tree -> Homepage (e.g., footer link)
CREATE (p20)-[:LINKS_TO {type: 'NAV_LINK', weight: 0.5}]->(p1) // Brave Knight -> Homepage (e.g., footer link);
```

### Step 3: Prepare the Graph for Analysis
To run GDS algorithms efficiently, we create an in-memory graph projection that includes all pages and their links, using the `weight` property for calculations.

**Cypher Code**:
```cypher
MATCH (n:Page)
OPTIONAL MATCH (n)-[:LINKS_TO]->(m:Page)
WITH gds.graph.project('seo_mesh_graph', n, m, {}) AS g
RETURN g.graphName;
```

### Step 4: Measure Page Importance with PageRank
We use the PageRank algorithm to calculate how important each page is based on the links pointing to it. In this demo, we run a biased (or personalized) PageRank, which is a variation of the standard algorithm that biases the random walk towards specific source nodes. This models a scenario where the "surfer" is more likely to restart their navigation from certain key pages, such as the homepage, categories, or blogs.

Why use biased PageRank? According to the Neo4j documentation on personalized PageRank, it's useful for tailoring importance scores to specific interests or goals, like in recommender systems or SEO optimization. The key difference from standard PageRank is the `sourceNodes` parameter, which allows you to specify starting nodes with optional bias weights (e.g., homepage at 1.0, categories at 0.5, blogs at 0.3). This biases the algorithm to prioritize authority flow from these nodes, better simulating real-user behavior on a website where visitors often start from high-level pages. In SEO terms, it helps emphasize connections to key product pages by enhancing their visibility through biased entry points, leading to more targeted boosts in search rankings.

This shows which pages have the most authority, like your homepage or main categories. You’ll see a ranked list of pages to understand your site’s current SEO strength.

**Cypher Code**:
```cypher
MATCH (home_page:Homepage)
WITH COLLECT (home_page) AS home_pages
MATCH (cat:Category)
WITH home_pages, COLLECT (cat) AS cats
MATCH (b:Blog)
WITH home_pages, cats, COLLECT (b) AS blogs
WITH [p IN home_pages | [p,1.0]]
      + [c IN cats | [c, 0.5]]
      + [b IN blogs | [b, 0.3]] AS source_nodes
CALL gds.pageRank.stream('seo_mesh_graph', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes
})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).url AS name, score
ORDER BY score DESC, name ASC
```

We persist the PageRank scores as a property on each node to analyze and use in the next steps.

```cypher
MATCH (home_page:Homepage)
WITH COLLECT (home_page) AS home_pages
MATCH (cat:Category)
WITH home_pages, COLLECT (cat) AS cats
MATCH (b:Blog)
WITH home_pages, cats, COLLECT (b) AS blogs
WITH [p IN home_pages | [p,1.0]]
      + [c IN cats | [c, 0.5]]
      + [b IN blogs | [b, 0.3]] AS source_nodes
CALL gds.pageRank.write('seo_mesh_graph', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes,
  writeProperty: 'pagerank'
}) YIELD nodePropertiesWritten, ranIterations
RETURN nodePropertiesWritten, ranIterations
```

### Step 5: Find Related Pages
To suggest new links, we look for pages with similar keywords (e.g., “mystery” and “thriller”). Using GDS’s node similarity algorithm, we identify pairs of pages that should be linked to improve relevance and SEO.

**Extract Keywords** (requires APOC):
```cypher
MATCH (p:Page)
UNWIND [kw IN apoc.text.split(p.keywords, ',') | trim(kw)] AS word
WITH p, word
MERGE (k:KeyWord {word:word})
MERGE (p)-[:HAS_KEYWORD]->(k)
```

**Project The Graph**:
```cypher
MATCH (n:Page|KeyWord)
OPTIONAL MATCH (n)-[r:LINKS_TO|HAS_KEYWORD]->(m:Page|KeyWord)
WITH gds.graph.project('pages_kw',n , m, {},{undirectedRelationshipTypes: ['*']}) AS g
RETURN g.graphName AS graph, g.nodeCount AS nodes, g.relationshipCount AS rels
````
**Compute Similarity**:
```cypher
CALL gds.nodeSimilarity.write('pages_kw', {
    writeRelationshipType: 'SIMILAR',
    writeProperty: 'score'
})
YIELD nodesCompared, relationshipsWritten
```

### Step 6: Add New Links and Check Impact

Here is how you can see some book's pagerank:

**Book's Pagerank**
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})
RETURN b.title AS title, b.pagerank AS page_rank
```
Based on the similarity analysis, add new `:LINKS_TO` relationships to connect related pages, focusing on boosting priority pages (e.g., book pages). Then, re-run PageRank (using the same biased configuration) to see how these links increase the authority of your target pages. This demonstrates the SEO impact of your optimizations.

**Add Links**: Manually create `:LINKS_TO` relationships based on the similarity output (e.g., link high-similarity pages to your target book pages).
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})-[r:SIMILAR]-(sim:Page)
WHERE r.score > 0.1
AND NOT EXISTS {(sim)-[rel:LINKS_TO WHERE rel.boost IS null]->(b)}
MERGE (sim)-[:LINKS_TO {boost:true}]->(b)
```

**Re-Run PageRank**: Repeat the PageRank query from Step 4 and compare the scores.

**Drop In-Memory Graph**:
```cypher
CALL gds.graph.drop('seo_mesh_graph')
// Then Project again and Write pagerank again
```

**Project New Mesh Into Memory**
```cypher
MATCH (n:Page)
OPTIONAL MATCH (n)-[:LINKS_TO]->(m:Page)
WITH gds.graph.project('seo_mesh_graph', n, m, {}) AS g
RETURN g.graphName;
```

**Write Pagerank again**
```cypher
MATCH (home_page:Homepage)
WITH COLLECT (home_page) AS home_pages
MATCH (cat:Category)
WITH home_pages, COLLECT (cat) AS cats
MATCH (b:Blog)
WITH home_pages, cats, COLLECT (b) AS blogs
WITH [p IN home_pages | [p,1.0]]
      + [c IN cats | [c, 0.5]]
      + [b IN blogs | [b, 0.3]] AS source_nodes
CALL gds.pageRank.write('seo_mesh_graph', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes,
  writeProperty: 'pagerank'
}) YIELD nodePropertiesWritten, ranIterations
RETURN nodePropertiesWritten, ranIterations
```

**Book's New Pagerank**
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})
RETURN b.title AS title, b.pagerank AS page_rank
```

## Get Started
1. Clone or download the code from the GitHub repository (https://github.com/halftermeyer/neo4j-seo-boost).
2. Open Neo4j Browser and run the Cypher queries in order.
3. Check the results to see which pages gain the most SEO benefit.

## Take It Further
- **Use Your Own Data**: Replace the bookstore example with your website’s pages (e.g., from a tool like Screaming Frog).
- **Explore Visually**: Try Neo4j Bloom to see your website’s graph and spot linking opportunities.
- **Customize**: Adjust link weights, bias values, or similarity thresholds to match your SEO goals.

## Learn More
For help or inspiration, check out the [Neo4j Documentation](https://neo4j.com/docs/) or join the [Neo4j Community](https://community.neo4j.com/). This demo is a starting point to see how Neo4j can transform your SEO strategy with the power of graphs!
