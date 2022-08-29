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

## Resources

![](<../../.gitbook/assets/immagine (5).png>)

### Books

* [Designing Data-Intensive Applications](https://smile.amazon.co.uk/dp/1449373321/) - Martin Kleppmann ([website](https://dataintensive.net/))
  * [Notes by Keyvan Akbary](https://keyvanakbary.github.io/learning-notes/books/designing-data-intensive-applications/)
  * [Review by Henrik Warne](https://henrikwarne.com/2019/07/27/book-review-designing-data-intensive-applications/)
* [System Design Interview – An insider's guide, Second Edition](https://smile.amazon.co.uk/dp/B08CMF2CQF/) - Alex Xu

### Courses

* [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
* [System Design Interview - An Insider's Guide](https://courses.systeminterview.com/courses/system-design-interview-an-insider-s-guide)

### GitHub repositories

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

### YouTube playlists

* [System Design](https://www.youtube.com/playlist?list=PLMCXHnjXnTnvo6alSjVkgxV-VH6EPyvoX) - Gaurav Sen
* [Tushar Roy - Coding Made Simple](https://www.youtube.com/watch?v=UzLMhqg3\_Wc\&list=PLrmLmBdmIlps7GJJWW9I7N0P0rB0C3eY2)
