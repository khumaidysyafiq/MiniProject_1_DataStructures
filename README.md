# Data Structures Mini Project 1: Music Playlist Manager

## Description
We often use music applications everyday, like Spotify, Youtube Music, etc. But behind those applications there is a lot of logic and code that makes it very nice to use, like the ability to see our listening history, what songs comes next, and the order in which we want to play it. In order for these features to exist, many data structures are needed, mainly stack, queue, and deque. This mini project aims to create a simple music playlist manager which can do the above mentioned features using data structures.   

## Overview   

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

5. PlaylistManager: Contains all data structures and playing song, connects all the program   
```c
typedef struct playlist_manager {
    Queue nextSong;           
    Stack history;        
    Deque favorites;       
    Song *currentlyPlaying; 
} PlaylistManager;
```

### Song Functions

1. createSong: Create new song by allocating memory   
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

2. freeSong: Free memory allocated for a song   
```c
void freeSong(Song *song) {
    if(song) {
        free(song);
    }
}
```

### Queue Functions   

1. initQueue: Nodes are null, size 0   
```c
void initQueue(Queue *q) {
    q->front = NULL;
    q->rear = NULL;
    q->size = 0;
}
```   

2. isEmptyQueue: Checks if queue is empty   
```c
bool isEmptyQueue(Queue *q) {
    return (q->front == NULL);
}
```   

3. enqueue: Add new song to back of queue   
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

4. dequeue: Removes current front song _(plays next song)_   
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

5. displayQueue: Shows all songs from front to back in queue
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

### Stack Functions   

1. initStack: Top is null, size 0
```c
void initStack(Stack *s) {
    s->top = NULL;
    s->size = 0;
}
```

2. isEmptyStack: Checks if stack is empty
```c
bool isEmptyStack(Stack *s) {
    return (s->top == NULL);
}
```

3. push: Add recently played songs to top of stack
```c
void push(Stack *s, Song *song) {
    Song *newNode = createSong(song->title, song->artist);
    if (newNode) {
        newNode->next = s->top;
        s->top = newNode;
        s->size++;
    }
}
```

4. pop: Removes song at the top of atack _(for playing previous song)_
```c
Song* pop(Stack *s) {
    if (isEmptyStack(s)) {
        return NULL;
    }
    
    Song *temp = s->top;
    Song *removedSong = createSong(temp->title, temp->artist);
    
    s->top = s->top->next;
    free(temp);
    s->size--;
    return removedSong;
}
```

5. displayStack: Shows all songs from top to bottom
```c
void displayStack(Stack *s) {
    if (isEmptyStack(s)) {
        printf("Empty\n\n");
        return;
    }
    
    Song *temp = s->top;
    while (temp != NULL) {
        printf("%s by %s", temp->title, temp->artist);
        if (temp->next != NULL) {
            printf("\n");
        }
        temp = temp->next;
    }
    printf("\n(Size: %d)\n\n", s->size);
}
```

### Deque Functions   

1. initDeque: Front and back are null, size 0
```c
void initDeque(Deque *d) {
    d->front = NULL;
    d->back = NULL;
    d->size = 0;
}
```

2. isDequeEmpty: Checks if deque is empty
```c
bool isEmptyDeque(Deque *d) {
    return (d->front == NULL);
}
```

3. pushFront: Add song to the front of deque/favorite list
```c
void pushFront(Deque *d, Song *song) {
    Song *newNode = createSong(song->title, song->artist);
    if (newNode) {
        if (isEmptyDeque(d)) {
            d->front = newNode;
            d->back = newNode;
        } else {
            newNode->next = d->front;
            d->front = newNode;
        }
        d->size++;
    }
}
```

4. pushBack: Add song to the back of deque/favorite list
```c
void pushBack(Deque *d, Song *song) {
    Song *newNode = createSong(song->title, song->artist);
    if (newNode) {
        if (isEmptyDeque(d)) {
            d->front = newNode;
            d->back = newNode;
        } else {
            d->back->next = newNode;
            d->back = newNode;
        }
        d->size++;
    }
}
```

5. popFront: Removes the front song _(plays next)_
```c
Song* popFront(Deque *d) {
    if (isEmptyDeque(d)) {
        return NULL;
    }
    
    Song *temp = d->front;
    Song *removedSong = createSong(temp->title, temp->artist);
    
    d->front = d->front->next;
    if (d->front == NULL) {
        d->back = NULL;
    }
    
    free(temp);
    d->size--;
    return removedSong;
}
```

6. popBack: Removes the back song _(plays next)_
```c
Song* popBack(Deque *d) {
    if (isEmptyDeque(d)) {
        return NULL;
    }
    
    if (d->front == d->back) {
        return popFront(d);
    }
    
    Song *temp = d->front;
    while (temp->next != d->back) {
        temp = temp->next;
    }
    
    Song *removedSong = createSong(d->back->title, d->back->artist);
    free(d->back);
    d->back = temp;
    d->back->next = NULL;
    d->size--;
    return removedSong;
}
```

7. displayDeque: Shows all favorite songs from front to back
```c
void displayDeque(Deque *d) {
    if (isEmptyDeque(d)) {
        printf("Empty\n\n");
        return;
    }
    
    Song *temp = d->front;
    while (temp != NULL) {
        printf("%s by %s", temp->title, temp->artist);
        if (temp->next != NULL) {
            printf("\n");
        }
        temp = temp->next;
    }
    printf("\n(Size: %d)\n\n", d->size);
}
```

### PlaylistManager Fucntions   

1. initPlaylist: Initializes all data structures, currently playing song is null
```c
void initPlaylist(PlaylistManager *pm) {
    initQueue(&pm->nextSong);
    initStack(&pm->history);
    initDeque(&pm->favorites);
    pm->currentlyPlaying = NULL;
}
```

2. addToPlaylist: Adds song to end of playlist _(queue)_   
```c
void addToPlaylist(PlaylistManager *pm, Song *song) {
    enqueue(&pm->nextSong, song);
    printf("Added %s by %s to end of playlist (Queue)\n\n", song->title, song->artist);
}
```

3. playNext: Plays next; removes front song of queue and saves it to top of stack
```c
void playNext(PlaylistManager *pm) {
    Song *next = dequeue(&pm->nextSong);
    
    if (next == NULL) {
        printf("No songs in playlist\n\n");
        return;
    }
    
    if (pm->currentlyPlaying != NULL) {
        push(&pm->history, pm->currentlyPlaying);
        printf("%s by %s moved to history\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
    }
    
    pm->currentlyPlaying = next;
    printf("Now playing: %s by %s\n\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
}
```

4. goBack: Plays previous song; pops from stack and adds to queue
```c
void goBack(PlaylistManager *pm) {
    if (isEmptyStack(&pm->history)) {
        printf("No songs in history\n\n");
        return;
    }
    
    if (pm->currentlyPlaying != NULL) {
        enqueue(&pm->nextSong, pm->currentlyPlaying);
        printf("%s by %s added back to playlist\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
        freeSong(pm->currentlyPlaying);
    }
    
    pm->currentlyPlaying = pop(&pm->history);
    printf("Still playing: %s by %s\n\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
}
```

5. addToFavorites: Adds song to favorite; new at front and classic at back
```c
void addToFavorites(PlaylistManager *pm, Song *song, bool asNewFavorite) {
    if (asNewFavorite) {
        pushFront(&pm->favorites, song);
        printf("Added %s by %s as NEW FAVORITE (front of deque)\n\n", song->title, song->artist);
    } else {
        pushBack(&pm->favorites, song);
        printf("Added %s by %s as CLASSIC FAVORITE (back of deque)\n\n", song->title, song->artist);
    }
}
```

6. playNewFavorites: Play new favorite song _(front of deque)_
```c
void playNewFavorites(PlaylistManager *pm) {
    if (isEmptyDeque(&pm->favorites)) {
        printf("No favorites\n\n");
        return;
    }
    
    Song *favSong = popFront(&pm->favorites);
    
    if (pm->currentlyPlaying != NULL) {
        push(&pm->history, pm->currentlyPlaying);
        printf("%s by %s moved to history\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
    }
    
    pm->currentlyPlaying = favSong;
    printf("Playing NEW FAVORITE: %s by %s\n\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
}
```

7. playClassicFavorites: Play classic favorite song _(back of deque)_
```c
void playClassicFavorites(PlaylistManager *pm) {
    if (isEmptyDeque(&pm->favorites)) {
        printf("No favorites\n\n");
        return;
    }
    
    Song *favSong = popBack(&pm->favorites);
    
    if (pm->currentlyPlaying != NULL) {
        push(&pm->history, pm->currentlyPlaying);
        printf("%s by %s moved to history\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
    }
    
    pm->currentlyPlaying = favSong;
    printf("Playing CLASSIC FAVORITE: %s by %s\n\n", pm->currentlyPlaying->title, pm->currentlyPlaying->artist);
}
```

8. displayStatus: Shows currently playing song and all data structures
```c
void displayStatus(PlaylistManager *pm) {
    if (pm->currentlyPlaying != NULL) {
        printf("NOW PLAYING:\n");
        printf("Title:  %s\n", pm->currentlyPlaying->title);
        printf("Artist: %s\n\n", pm->currentlyPlaying->artist);
    } else {
        printf("NOW PLAYING:\n");
        printf("No song playing\n\n");
    }
    
    printf("UP NEXT (Queue):\n");
    displayQueue(&pm->nextSong);
    
    printf("HISTORY (Stack):\n");
    displayStack(&pm->history);
    
    printf("FAVORITES (Deque):\n");
    displayDeque(&pm->favorites);
}
```

### Main Menu   

Interface where users can choose what action they want to do

```c
void displayMenu() {
    printf("MUSIC PLAYLIST MANAGER\n");
    printf("1. Add song to playlist (Queue)\n");
    printf("2. Play next song\n");
    printf("3. Go back to previous song (Stack)\n");
    printf("4. Add song to favorites (New / Classic) (Deque)\n");
    printf("5. Play from favorites - NEW (front)\n");
    printf("6. Play from favorites - CLASSIC (back)\n");
    printf("7. Show current status\n");
    printf("8. Exit\n");
    printf("Choice: ");
}
```   
