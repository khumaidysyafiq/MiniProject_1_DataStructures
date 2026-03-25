# Data Structures Mini Project 1: Music Playlist Manager

## Description
We often use music applications everyday, like Spotify, Youtube Music, etc. But behind those applications there is a lot of logic and code that makes it very nice to use, like the ability to see our listening history, what songs comes next, and the order in which we want to play it. In order for these features to exist, many data structures are needed, mainly stack, queue, and deque. This mini project aims to create a simple music playlist manager which can do the above mentioned features using data structures.   

## Mini Project Overview   

### Features   

1. Playlist   
   In the playlist, users can add a song to the queue, check the next song, and see all songs in queue.

2. History   
   User can see history of previously played songs and replay a previuosly played song by putting it back to the queue.

3. Favourites   
   A separate playlist for favorite songs, users can play these songs immediately without waiting for the queue.

### Data Structures Explanation   

1. Queue   
   Follows FIFO principle, used for the playlist feature. _(User adds a song, that will be played first)_

2. Stack   
   Follows LIFO principle, used for the history feature. _(If user wants to play previously played song)_

3. Deque   
   Used for favorites feature, user can add new or classic favorite songs on both ends.

## Code and Explanation   

### Structures   

1. Song: Represents a single song and pointer to next song   
```c
typedef struct song {
    char title[100];
    char artist[100];
    struct song *next;
} Song;
```   

2. Queue: FIFO, song will be played in order added   
```c
typedef struct queue {
    Song *front;
    Song *back;
    int size;
} Queue;
```   

3. Stack: LIFO stack array, song last played will be first to go back   
```c
typedef struct stack {
    Song *top;
    int size;
} Stack;
```

4. Deque: For favorites, can add song at front/back   
```c
typedef struct deque {
    Song *front;
    Song *back;
    int size;
} Deque;
```

5. Playlist Manager: Contains all data structures and playing song, connects all the program   
```c
typedef struct playlist_manager {
    Queue nextSong;           
    Stack history;        
    Deque favorites;       
    Song *currentlyPlaying; 
} PlaylistManager;
```

### Song Functions

1. Create Song: Create new song by allocating memory   
```c
Song* createSong(char *title, char *artist) {
    Song *newSong = (Song*) malloc(sizeof(Song));
    if (newSong) {
        strncpy(newSong->title, title, 99);
        newSong->title[99] = '\0';

        strncpy(newSong->artist, artist, 99);
        newSong->artist[99] = '\0';

        newSong->next = NULL;
    }
    return newSong;
}
```

2. Free Song: Free memory allocated for a song   
```c
void freeSong(Song *song) {
    if(song) {
        free(song);
    }
}
```

### Queue Functions   

1. Initialize: Nodes are null, size 0   
```c
void initQueue(Queue *q) {
    q->front = NULL;
    q->rear = NULL;
    q->size = 0;
}
```   

2. Empty Check: Checks if queue is empty   
```c
bool isEmptyQueue(Queue *q) {
    return (q->front == NULL);
}
```   

3. Enqueue: Add new song to back of queue   
```c
void enqueue(Queue *q, Song *song) {
    Song *newNode = createSong(song->title, song->artist);
    if (newNode) {
        if (isEmptyQueue(q)) {
            q->front = newNode;
            q->back = newNode;
        } else {
            q->back->next = newNode;
            q->back = newNode;
        }
        q->size++;
    }
}
```   

4. Dequeue: Removes current front song _(plays next song)_   
```c
Song* dequeue(Queue *q) {
    if (isEmptyQueue(q)) {
        return NULL;
    }
    
    Song *temp = q->front;
    Song *removedSong = createSong(temp->title, temp->artist);
    
    q->front = q->front->next;
    if (q->front == NULL) {
        q->back = NULL;
    }
    
    free(temp);
    q->size--;
    return removedSong;
}
```   

5. Display: Shows all songs from front to back in queue
```c
void displayQueue(Queue *q) {
    if (isEmptyQueue(q)) {
        printf("Empty\n\n");
        return;
    }
    
    Song *temp = q->front;
    while (temp != NULL) {
        printf("%s by %s", temp->title, temp->artist);
        if (temp->next != NULL) {
            printf("\n");
        }
        temp = temp->next;
    }
    printf("\n(Size: %d)\n\n", q->size);
}
```

