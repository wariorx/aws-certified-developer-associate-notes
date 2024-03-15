# Elasticache
* Serverless Refis and memcached compatible caching service delivering real time performance for modenr applications
* Scales to hundreds of millions of operations per second wiht microsecond response times
* Removes the need to plan and manage caching capacity - it constatly monitors the cache's memory, compute, and network bandwidth in use and scales to meet the application
* Applications may connect to Elasticache through VPC Endpoints that are created along with the Elasticache cluster
* Connect to Elasticache from an application using a single DNS endpoint, abstracting client topology from the client
  * You may self-design clusters by choosing a node family, size, and number of nodes for fine-grained control (including operation in non-cluster mode with a single shard)
  
## Redis
```python
redis_client = redis.Redis(host=elasticache_endpoint, port=6379, credential_provider=creds_provider, ssl=True, ssl_cert_reqs="none")
redis_client.set(key, uuid_in)
result = redis_client.get(key)
decoded_result = result.decode("utf-8")
```

## Memcached
```python
memcached_client = Client((elasticache_config_endpoint, target_port), tls_context=context)
memcached_client.set("uuid", uuid_in, expire=500, noreply=False)
# get the item (UUID) from the cache
result = memcached_client.get("uuid")
decoded_result = result.decode("utf-8")
```

## Caching strategies
* **Lazy loading**: load data into the cache only when necessary;
  * Request data to elasticache first
  * If the data is not in elasticache or has expired, the app must request from the data store
  * Data received from the data store is then stored in elasticache by the application
  * **Pros**: only requested data is cached thus saving memory. Node failures are not fatal since the application can continue to function
  * **Cons**: cache miss performance penalty. Stale data (only write on miss, if hits continue new data won't be populated)
  ```python
  get_customer(customer_id)

    customer_record = cache.get(customer_id)
    if (customer_record == null)
    
        customer_record = db.query("SELECT * FROM Customers WHERE id = {0}", customer_id)
        cache.set(customer_id, customer_record)
    
    return customer_record
  ```
* **Write-through**: add data into the cache whenever data is written to the database
  * **Pros**: data is never stale. Write penalty (dobule writing)
  * **Cons**: missing data on new node spin up (can be mitigated by combining lazy loading and write-through strategies). Cache churn, since most data won't bne read again (can be mitigted by adding TTL to cache entries)
  ```python
  save_customer(customer_id, values)

  customer_record = db.query("UPDATE Customers WHERE id = {0}", customer_id, values)
  cache.set(customer_id, customer_record)
  return success
  ```
* **Adding TTL**: gain advantages of other strategies while preventing  cache chum by setting a Time To Live in seconds that determines the expiration for each key in the cache
  ```python
  save_customer(customer_id, values)

  customer_record = db.query("UPDATE Customers WHERE id = {0}", customer_id, values)
  cache.set(customer_id, customer_record, 300)

  return success
  ```

## Choosing an Engine

* **Memcached**: 
  * You need the simplest model possible.
  * You need to run large nodes with multiple cores or threads.
  * You need the ability to scale out and in, adding and removing nodes as demand on your system increases and decreases.
  * You need to cache objects.
* **Redis**:
  * You need any advanced REDIS features or functions
  * You need complex data types, such as strings, hashes, lists, sets, sorted sets, and bitmaps.
  * You need to sort or rank in-memory datasets.
  * You need persistence of your key store.
  * You need to replicate your data from the primary to one or more read replicas for read intensive applications.
  * You need automatic failover if your primary node fails.
  * You need publish and subscribe (pub/sub) capabilities—to inform clients about events on the server.
  * You need backup and restore capabilities.
  * You need to support multiple databases.