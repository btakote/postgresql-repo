# postgresql-repo
MULTI-VERSION Concurrency Control (MVCC)

Multi version concurrency control. Features PostgreSQL uses to deliver read consistency.

Deleting/updating:  
When data is being modified, a snapshot of the data in the table is made and kept on that same table for read consistency. 
This also prevents unnecessary locking because readers will not block writers and writers will not block readers.

On the table, we have both live data (actual data) and dead data(dead tuples= snapshot before modification). 
Each time vacuumin runs,  it removes dead turples, indexes and marks the space as available for future reuse.  
However, it will not return the space to the operating system. It rather  map it to a table called table_fsm(free space mapping). 

The live data will be mapped to another table called  table_vm(visibility mapping)  because before each run, 
autovacuum needs to scan this table before it runs to know the exact location of live data and  will just pick up dead data and clean them out. 
This helps to avoid a full table scan and  improves Performance.

After each run, Auto vacuum  updates the free space mapping.
Visibility Mapping for each table is updated to keep track of which pages contain only tuples that are known to be visible 
to all active transactions (and all future transactions, until the page is again modified).
