# System Design Components

System Design Components - Notes

1. Consistent Core
   1. Problem
      1. When a cluster grows, huge number of servers comes to be the part of the cluster.
      2. This causes important information about the servers to be available in a consistent manner.
      3. This is termed as a need for Linearizability.
      4. On other hand, the information needs to stay fault - tolerant.
      5. This can be achieved using Quorum based approach, however the throughput reduces with increasing number of servers
   2. Solution 
      1. A cluster with 3 - 5 nodes are built.
      2. Being small in size, the cluster ensure consistency and fault tolerance.
      3. This cluster functions as the metadata store for the bigger (or functional) cluster which can grow in size without worrying about the metadata information
   3. Low Level Detail
      1. Metadata Storage 
         1. The smaller cluster uses Quorum based approach to replicate the key value information that it stores. The cluster uses RAFT type protocols to achieve consensus.
         2. Supporting hierarchical storage
            1. The cluster supports registration for servers.
            2. The servers (clients) can store KV information while prefixing the key with the server identity
         3. Client interaction
            1. Finding the leader
               1. Client needs to connect to the leader of the metadata cluster
                  1. approach#1 : every node (that are follower) has the knowledge of current leader, therefore, when a client joins a follower node, it returns the address of the leader back to the client, thus helping the client to connect directly
                  2. approach#2 : the nodes have a forwarding mechanism implemented so that they are able to redirect to the current leader
            2. Handling duplicate request 
               1. Client can find a leader being non - responsive and thus make new request. 
               2. The leader coming back will get two requests to process.
               3. To avoid duplicate processing, idempotency needs to be built in
