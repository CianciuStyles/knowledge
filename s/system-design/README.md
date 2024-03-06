# System Design

## Notes

### System Design Interview: An Insider's Guide - Alex Xu

#### Chapter 1 - Scale from zero to millions of users

1. Start with a single server setup
2. Move the database to a separate server (consider carefully SQL vs. NoSQL)
3. Add a load balancer to enable horizontal scaling
4. Set up database replication (with master write / slave read)
5. Caching to improve performance (memory vs. disk trade-off)
6. Content Delivery Network for static assets and/or geographical latency
7. Stateless web tier to avoid sticky sessions
8. Data centers for geographical latency / redundancy
9. Message queues for asynchronous processing
10. Logging / metrics for identifying and resolving problems
11. Sharding for database horizontal scaling (celebrity problem)

#### Chapter 2 - Back-of-the-envelope estimation

1. Read from memory: 250 µs
2. RTT in the same data center: 500 µs
3. Read from network: 10 ms
4. Read from disk: 30 ms
5. RTT internationally: 150 ms
6. Use rounding / approximation to make calculations easiers
7. Write down the assumptions you're making
8. Always label the units

#### Chapter 3 - A framework for system design interviews

1. Understand the problem and establish the design scope
   1. What features?
   2. How many users?
   3. Anticipation of scale?
   4. Technology stack?
   5. Existing services?
2. Propose high level design and get buy-in
   1. Initial blueprint
   2. Ask for feedback
   3. Draw boxes diagrams with components
   4. Back-of-the-envelope calculations to check scale
3. Design deep dive
4. Wrap up
   1. Bottlenecks
   2. Potential Improvements
   3. Recap
   4. Next scale curve

#### Chapter 4 - Design a rate limiter

1. Client-side, server-side, or middleware
2. Send back 429 Too Many Requests when throttled
3. Algorithms for rate limiting
   1. Token bucket
   2. Leaky bucket
   3. Fixed window counter
   4. Sliding window log / counter
4. In-memory cache supports time-based expiration
5. Rules for limiting in cache / disk
6. Race conditions / synchronization

#### Chapter 5 - Design consistent hashing

1. `hash(key) % N`
   1. good for fixed-size pools
   2. bad when servers are added / removed
2. Consistent Hashing: only K / N keys need to be remapped
3. Introduce a hash ring and hash server / nodes onto it
4. Server lookup is done clockwise on the ring
5. Problems with partition size and non-uniform distribution
   1. Solution: virtual nodes (more nodes, better balance)

#### Chapter 6 - Design a key-value store

1. Basic approach: in-memory hash table -> does not scale
2. CAP theorem: pick two among consistency, availability and partition tolerance
3. CP -> blocking write for accuracy
4. AP -> eventual consistency
5. Data replication on K server
6. Resolve incosistencies using vector clocks
7. Gossip protocol to handle failure
8. Merkle trees to detect inconsistencies and calculating smallest deltas
9. Coordinator node as proxy between client and store

#### Chapter 7 - Design a unique ID generator in distributed systems

1. Auto increment in relational database -> does not scale
2. Multi-master replication (auto\_increment with K) -> problem when adding / removing nodes
3. UUID -> not numeric, don't go up with time
4. Centralised ticket server -> single point of failure
5. Snowflake approach: f(timestamp, data center ID, machine ID, sequence number)
6. Clock synchronisation?
7. Section length tuning?

#### Chapter 8 - Design a URL shortener

1. REST APIs with URL redirecting (Location header, temporal vs. permanent redirect)
2. Intuitive approach: hash table (URL -> `tinyurl.com/${hash(URL)}`) -> does not scale
3. `<shortURL, longURL>` tuples in RDBMS
4. Hash + collision resolution vs. base62 conversion
5. Rate limiting
6. Analytics
7. Availability vs. consistency

#### Chapter 9 - Design a web crawler

1. Parallelisation to achieve scalability
2. Robustness for bad HTML, malicious links, unresponsive servers
3. Politeness: do not overload servers while indexing
4. Start from seed URL (divide URLs by country / topic)
5. URL frontier to keep track of websites to visit
6. Content parser to filter out malformed pages
7. Content deduplicator to avoid visiting mirrors / duplications
8. URL extractor to progress in the crawl
9. URL deduplicator to avoid visiting the same URL multiple times
10. BFS vs. DFS: depth can grow pretty quickly
11. Different queues per domain for politeness
12. Priority queues for proritising important content

#### Chapter 10 - Design a notification system

1. On app install or sign up, collect mobile token, phone number and email address
2. Decouple notification systems from actuators (iOS, Android, SMS, email servers) through message queues
3. Prevent data loss through at-least once delivery (with retries)
4. Templates to avoid building content from scratch
5. Respect user preferences with respect to communication channels
6. Rate limiting
7. Event tracking / analytics

#### Chapter 11 - Design a news feed system

1. Feed publishing -> fanout on write (good for regular people)
2. Feed retrieval -> fanout on read (good for celebrities with caching)
3. Use graph DB to store / retrieve user connections
4. Use a CDN for media files (photos / videos)

#### Chapter 12 - Design a chat system

1. Clients connect to chat servers via WebSockets for two-ways communication (send/receive messages)
2. Other operations (login, group management, user profile) are stateless and can be done over HTTP
3. Notification service for newer message
4. Chat data is very large, read-to-write ratio is 1:1 -> prefer key-value store over SQL
5. Service discovery to find out chat server to connect to
6. One message sync queue per user -> either delivery the message or store them when offline
7. Online presence through heartbeat messages
8. End-to-end encryption of messages
9. Caching messages on the client side

#### Chapter 13 - Design a search autocomplete system

1. One request per input character, with low latency
2. Data gathering system, takes input queries and aggregates them (real-time or batch)
3. Query service, given a prefix return the most 5 searched items
4. Using prefix trees / tries is crucial for scalability
5. Limiting max length for query makes it a O(1) operation
6. Cache top queries at each node to avoid full traversal
7. Tries are not suited for SQL, better to use document store or key-value store (where the key is the prefix and the value is the trie node)
8. AJAX requests to save a full page re-render
9. Browser caching for data changing infrequently
10. Store tries in CDNs for local queries

#### Desing 14 - Design YouTube

1. Use CDN (expensive!) for streaming videos, API servers for other operations
2. Video uploading: original storage, transcoding servers (multiple formats and bit rates), transcoded storage, CDN, metadata servers
3. Video streaming: access metadata servers for search, then CDN for playback
4. Pre-signed uploads and DRM/encryption for security
5. Send only popular videos to CDN to save costs
6. CDN also for geographical content (popular only in one country)

#### Capitolo 15 - Design Google Drive

1. Reliability is extremely important (data loss in unacceptable)
2. Bandwidth usage needs to be contained
3. Web server to handle upload/download
4. Database to keep track of metadata
5. Storage system for actual files (e.g., S3) with cold storage for inactive data
6. The block server analyses deltas between versions and only sends changed blocks (saves bandwidth)
7. Needs strong consistency by default (different clients must see the same file)
8. Notification service via WebSockets for updates
9. De-duplicate blocks to save on storage data

## Resources

![](<../../.gitbook/assets/immagine (1).png>)

### Articles

* [5 Caching Mechanisms to Speed Up Your Application](https://hackernoon.com/5-caching-mechanisms-to-speed-up-your-application) - Pragati Verma
* [Consistent hashing algorithm](http://highscalability.com/blog/2023/2/22/consistent-hashing-algorithm.html) - High Scalability
* [Consistent Hashing Explained](https://systemdesign.one/consistent-hashing-explained/) - SystemDesign
* [Load Balancing](https://samwho.dev/load-balancing/) - Sam Rose
* [What is a content delivery network (CDN)?](https://www.cloudflare.com/en-gb/learning/cdn/what-is-a-cdn/) - CloudFlare

### Books

* [Designing Data-Intensive Applications](https://smile.amazon.co.uk/dp/1449373321/) - Martin Kleppmann ([website](https://dataintensive.net/))
  * [Notes by Keyvan Akbary](https://keyvanakbary.github.io/learning-notes/books/designing-data-intensive-applications/)
  * [Review by Henrik Warne](https://henrikwarne.com/2019/07/27/book-review-designing-data-intensive-applications/)
* [System Design Interview – An insider's guide, Second Edition](https://smile.amazon.co.uk/dp/B08CMF2CQF/) - Alex Xu

### Courses

* [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
* [System Design](https://www.karanpratapsingh.com/courses/system-design) - Karan Pratap Singh
* [System Design Interview - An Insider's Guide](https://courses.systeminterview.com/courses/system-design-interview-an-insider-s-guide)

### GitHub repositories

* [Awesome Scalability](https://github.com/binhnguyennus/awesome-scalability)
* [Awesome System Design](https://github.com/madd86/awesome-system-design)
* [Awesome System Design Resources](https://github.com/ashishps1/awesome-system-design-resources)
* [Byte-sized System Design Concepts](https://github.com/ByteByteGoHq/system-design-101) - ByteByteGo
* [CDN Up and Running](https://github.com/leandromoreira/cdn-up-and-running) - Leandro Moreira
* [grokking-system-design](https://github.com/lei-hsia/grokking-system-design)
* [Latency Numbers Every Programmer Should Know](https://gist.github.com/jboner/2841832)
* [software-architecture-books](https://github.com/mhadidg/software-architecture-books)
* [system-design-primer](https://github.com/donnemartin/system-design-primer)

### Videos

* [Scalability](https://www.youtube.com/watch?v=-W9F\_\_D3oY4) - David Malan

### Websites

* [High Scalability](http://highscalability.com/)
  * [Designing WhatsApp (2022)](http://highscalability.com/blog/2022/1/3/designing-whatsapp.html)
  * [Google's architecture (2008)](http://highscalability.com/google-architecture)
  * [YouTube's architecture (2008)](http://highscalability.com/youtube-architecture)
* [HowVideo.works](https://howvideo.works/)
* [Low Level Design (LLD) Coding](https://lldcoding.com/)
* [System Design](https://systemdesign.one/)
* [System Design Bytes for Enthusiasts](https://codersguild.github.io/System-Design/) ([GitHub](https://github.com/codersguild/System-Design))

### YouTube playlists

* [System Design](https://www.youtube.com/playlist?list=PLMCXHnjXnTnvo6alSjVkgxV-VH6EPyvoX) - Gaurav Sen
* [Tushar Roy - Coding Made Simple](https://www.youtube.com/watch?v=UzLMhqg3\_Wc\&list=PLrmLmBdmIlps7GJJWW9I7N0P0rB0C3eY2)
