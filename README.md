# Mini Project 1 Data Structures

## Topic: Music Playlist Manager   
We often use music applications everyday, like Spotify, Youtube Music, etc. But behind those applications there is a lot of logic and code that makes it very nice to use, like the ability to see our listening history, what songs comes next, and the order in which we want to play it. In order for these features to exist, many data structures are needed, mainly stack, queue, and deque. This mini project aims to create a simple music playlist manager which can do the above mentioned features using data structures.   

## Mini Project Overview   

### Features   

1. Playlist:   
   In the playlist, users can add a song to the queue, check the next song, and see all songs in queue.

2. History:   
   User can see history of previously played songs and replay a previuosly played song by putting it back to the queue.

3. Favourites:   
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

3. Stack: LIFO, song last played will be first to go back   
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
