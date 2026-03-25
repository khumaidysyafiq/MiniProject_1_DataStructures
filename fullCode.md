# Full Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>

typedef struct song {
    char title[100];
    char artist[100];
    struct song *next;
} Song;

typedef struct queue {
    Song *front;
    Song *back;
    int size;
} Queue;

typedef struct stack {
    Song *top;
    int size;
} Stack;

typedef struct deque {
    Song *front;
    Song *back;
    int size;
} Deque;

typedef struct playlist_manager {
    Queue nextSong;           
    Stack history;        
    Deque favorites;       
    Song *currentlyPlaying; 
} PlaylistManager;

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

void freeSong(Song *song) {
    if(song) {
        free(song);
    }
}

/*void copySong(Song *dest, Song *source) {
    if (dest && source) {
        strcpy(dest->title, source->title);
        strcpy(dest->artist, source->artist);
        dest->next = NULL;
    }
}*/

void initQueue(Queue *q) {
    q->front = NULL;
    q->back = NULL;
    q->size = 0;
}

bool isEmptyQueue(Queue *q) {
    return (q->front == NULL);
}

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

/*Song* peekQueue(Queue *q) {
    if (isEmptyQueue(q)) {
        return NULL;
    }
    return q->front;
}*/

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

void initStack(Stack *s) {
    s->top = NULL;
    s->size = 0;
}

bool isEmptyStack(Stack *s) {
    return (s->top == NULL);
}

void push(Stack *s, Song *song) {
    Song *newNode = createSong(song->title, song->artist);
    if (newNode) {
        newNode->next = s->top;
        s->top = newNode;
        s->size++;
    }
}

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

/*Song* peekStack(Stack *s) {
    if (isEmptyStack(s)) {
        return NULL;
    }
    return s->top;
}*/

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

void initDeque(Deque *d) {
    d->front = NULL;
    d->back = NULL;
    d->size = 0;
}

bool isEmptyDeque(Deque *d) {
    return (d->front == NULL);
}

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

void initPlaylist(PlaylistManager *pm) {
    initQueue(&pm->nextSong);
    initStack(&pm->history);
    initDeque(&pm->favorites);
    pm->currentlyPlaying = NULL;
}

void addToPlaylist(PlaylistManager *pm, Song *song) {
    enqueue(&pm->nextSong, song);
    printf("Added %s by %s to end of playlist (Queue)\n\n", song->title, song->artist);
}

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

void addToFavorites(PlaylistManager *pm, Song *song, bool asNewFavorite) {
    if (asNewFavorite) {
        pushFront(&pm->favorites, song);
        printf("Added %s by %s as NEW FAVORITE (front of deque)\n\n", song->title, song->artist);
    } else {
        pushBack(&pm->favorites, song);
        printf("Added %s by %s as CLASSIC FAVORITE (back of deque)\n\n", song->title, song->artist);
    }
}

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

int main() {
    PlaylistManager pm;
    initPlaylist(&pm);
    
    int choice;
    char title[100];
    char artist[100];
    int favType;
    
    do {
        displayMenu();
        scanf("%d", &choice);
        printf("\n");
        
        switch (choice) {
            case 1: 
                printf("Song title: ");
                scanf(" %[^\n]s", title);
                
                printf("Artist name: ");
                scanf(" %[^\n]s", artist);
                
                Song *newSong = createSong(title, artist);
                addToPlaylist(&pm, newSong);
                freeSong(newSong); 
                break;
                
            case 2:
                playNext(&pm);
                break;
                
            case 3:
                goBack(&pm);
                break;
                
            case 4: 
                printf("Song title: ");
                scanf(" %[^\n]s", title);
                
                printf("Artist name: ");
                scanf(" %[^\n]s", artist);
                
                printf("Add as:\n");
                printf("1. NEW favorite (front)\n");
                printf("2. CLASSIC favorite (back)\n");
                printf("Choice: ");
                scanf("%d", &favType);
                
                Song *favSong = createSong(title, artist);
                addToFavorites(&pm, favSong, (favType == 1));
                freeSong(favSong);
                break;
                
            case 5:
                playNewFavorites(&pm);
                break;
                
            case 6:
                playClassicFavorites(&pm);
                break;
                
            case 7:
                displayStatus(&pm);
                break;
                
            case 8:
                printf("Goodbye!\n\n");
                break;
                
            default:
                printf("Invalid choice\n");
        }
        
    } while (choice != 8);
    
    return 0;
}
```
