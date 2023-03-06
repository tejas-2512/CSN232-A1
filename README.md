# Starve-Free-Reader-Writer-Problem
The star-free reader-writer problem is a classic challenge in computer science that focuses on managing access to a shared resource. This problem arises when multiple processes concurrently access a critical section of a shared resource, such as a database or a file. The challenge is to ensure that data consistency is maintained, and race conditions are avoided, while allowing concurrent access to the shared resource and preventing starvation.

In this problem, only one writer can access the critical section at any given point in time, while multiple readers can access it simultaneously. Synchronization is achieved using semaphores, but this may lead to starvation of either the readers or the writers. "Star-free" algorithms that prevent starvation are not allowed in this challenge. Researchers have proposed numerous solutions to the star-free reader-writer problem over the years, making it a highly active area of research in computer science.

## Starve-Free Solution
The solution to this problem is obtained by implementing the principle of FCFS using three semaphores:
1. common_mutex: Common semaphore used by both reader and writer that ensures equality and avoids starvation of one category.
2. reader: It is used to avoid the simultaneous modification of the reader_count variable by two readers.
3. modify_doc: It is used to avoid the modification of the document when a reader is reading


```cpp
//INITIALISIATION
int reader_count = 0;
semaphore common_mutex = 1;
semaphore reader_mutex = 1;
semaphore modify_doc = 1;

void Reader() {
    // Function to be executed at the arrival of a new reader 

    wait(common_mutex); 
    // Common semaphore for both reader and writer to maintain the FCFS principle
    // Next reader cannot proceed further if any writer is present that blocks this semaphore
    
    wait(reader); 
    // Semaphore to block two readers from changing the count simultaneously 

    ++reader_count;

    if(read_count == 1) wait(modify_doc); 
    // First reader blocks any writer to edit the document when it enters the critical section
        
    signal(reader);

    signal(common_mutex);
    // Common semaphore freed can be used by both reader or writer to begin it's process

        // * CRITICAL SECTION * //

    wait(reader);
    // Reader waits if any other reader is changing the count

    --reader_count;
    signal(reader);

    if(reader_count == 0){
        signal(modify_doc);
        // Last reader frees the modify_doc to allow access to critical section
    }

}

void Writer() {
    // Function to be executed at the arrrival of a new writer

    wait(common_mutex);
    // Common semaphore for both reader and writer to maintain the FCFS principle

    wait(modify_doc);
    // Writer blocks any reader to read until it edits the document
    // Semaphore used to enter the critical section

    signal(common_mutex);
    // Common semaphore freed can be used by both reader or writer to begin it's process

        // * CRITICAL SECTION * //


    signal(modify_doc);
    //  Now critical section can be accessed by the wating reader/writerC
}
```

## Correctness of the two Starve-Free Solutions
To ensure that a solution to the reader-writer problem is starve-free, it must satisfy three criteria: mutual exclusion, progress, and bounded waiting. In this context, we will verify if the two approaches described above satisfy these three criteria.
### Mutual Exclusion
To ensure mutual exclusion in the reader-writer problem, only one writer process or multiple reader processes, but no writer process, must access the critical section at any given time. This is accomplished using the common_mutex semaphore, which treats reader and writer processes equally and gives entry to them on a first-come, first-served (FCFS) basis. The semaphore ensures that a writer process can enter the critical section only when it is free, and a reader process can enter the critical section when other reader processes are present as well. Thus, the mutual exclusion criterion is satisfied.

### Progress
This code has been designed in a way that eliminates the possibility of a deadlock in our system's operation. Additionally, both the readers and writers complete their tasks within a limited timeframe, and semaphores are promptly released upon completion of their execution in the critical section. This allows other processes to enter the critical section and fulfill their tasks, thereby fulfilling the progress criterion.

### Bounded Waiting
Previously, writers would be at risk of starvation if readers came one after another, but with the introduction of the common_mutex semaphore, they are now queued up and released sequentially. Likewise, readers are also granted the opportunity to enter the critical section as a group whenever they reach the front of the queue. This means that neither readers nor writers have to wait for an indefinite period, thereby meeting the bounded waiting criterion.
