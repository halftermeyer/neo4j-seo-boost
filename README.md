# neo4j-seo-boost

This demo showcases how to use Neo4j to optimize a website's internal linking structure for better SEO performance.  
It focuses on creating semantically relevant links between pages while strategically boosting the visibility of specific product pages using PageRank.

## Goals
- Improve internal linking by identifying similar pages.
- Redirect PageRank to high-priority product pages.
- Generate links that are both natural and SEO-effective.

## Workflow Overview
1. Ingest website pages into the Neo4j graph.
2. Identify potential linking strategies based on similarity.
3. Calculate PageRank to determine importance flow.
4. Write back scores and create new optimized links.
5. Visualize and analyze the new graph structure.

---

## Step 1: Ingest Website Content

We begin by importing pages into the graph. Each page is labeled according to its type (Homepage, Category, Subcategory, Book) and enriched with metadata such as title, URL, and keywords.

### ingestion
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

---

## Step 2: Create Similarity-Based Links

We identify and link pages with similar keywords. This simulates realistic internal linking based on content similarity.

### project mesh into memory
```cypher
MATCH (n:Page)
OPTIONAL MATCH (n)-[:LINKS_TO]->(m:Page)
WITH gds.graph.project('maillage', n, m, {}) AS g
RETURN g.graphName;
```

---

## Step 3: Compute PageRank

Using the homepage as the entry point, we calculate the importance of each page through the graph’s structure.

### compute pagerank
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
CALL gds.pageRank.stream('maillage', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes
})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).url AS name, score
ORDER BY score DESC, name ASC
```

---

## Step 4: Write Back PageRank Scores

We persist the PageRank scores as a property on each node to analyze and use in the next steps.

### write pagerank
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
CALL gds.pageRank.write('maillage', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes,
  writeProperty: 'pagerank'
}) YIELD nodePropertiesWritten, ranIterations
RETURN nodePropertiesWritten, ranIterations
```

---

## Step 5: Boost Key Product Pages

We artificially increase the PageRank score of high-priority product pages (e.g., those to promote in SEO strategy).

### extract keywords as node
```cypher
MATCH (p:Page)
UNWIND [kw IN apoc.text.split(p.keywords, ',') | trim(kw)] AS word
WITH p, word
MERGE (k:KeyWord {word:word})
MERGE (p)-[:HAS_KEYWORD]->(k)
```

### Project Page--KeyWord into memory
```cypher
MATCH (n:Page|KeyWord)
OPTIONAL MATCH (n)-[r:LINKS_TO|HAS_KEYWORD]->(m:Page|KeyWord)
WITH gds.graph.project('pages_kw',n , m, {},{undirectedRelationshipTypes: ['*']}) AS g
RETURN g.graphName AS graph, g.nodeCount AS nodes, g.relationshipCount AS rels
```

### Compute node similarity
```cypher
CALL gds.nodeSimilarity.write('pages_kw', {
    writeRelationshipType: 'SIMILAR',
    writeProperty: 'score'
})
YIELD nodesCompared, relationshipsWritten
```

### Some book pagerank
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})
RETURN b.title AS title, b.pagerank AS page_rank
```


### How to boost?
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})-[r:SIMILAR]-(sim:Page)
WHERE r.score > 0.1
AND NOT EXISTS {(sim)-[r:LINKS_TO WHERE r.boosted IS NULL]->(b)}
RETURN DISTINCT b, sim, r.score
ORDER BY sim.pagerank DESC
```

### Topological boost
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})-[r:SIMILAR]-(sim:Page)
WHERE r.score > 0.1
AND NOT EXISTS {(sim)-[rel:LINKS_TO WHERE rel.boost IS null]->(b)}
MERGE (sim)-[:LINKS_TO {boost:true}]->(b)
```

## Drop in memory graph and compute again

### Drop graph
```cypher
CALL gds.graph.drop('maillage')
// Then Project again and Write pagerank again
```

### project mesh into memory
```cypher
MATCH (n:Page)
OPTIONAL MATCH (n)-[:LINKS_TO]->(m:Page)
WITH gds.graph.project('maillage', n, m, {}) AS g
RETURN g.graphName;
```

### write pagerank
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
CALL gds.pageRank.write('maillage', {
  maxIterations: 20,
  dampingFactor: 0.85,
  sourceNodes: source_nodes,
  writeProperty: 'pagerank'
}) YIELD nodePropertiesWritten, ranIterations
RETURN nodePropertiesWritten, ranIterations
```

### Same book pagerank
```cypher
MATCH (b:Book {title: "Stars of Tomorrow"})
RETURN b.title AS title, b.pagerank AS page_rank
```

This concludes the demo. You can build an optimized, semantically rich internal linking structure that strategically favors key product pages.
