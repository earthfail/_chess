# _chess
c# code for playing chess
#include <stdio.h>
//this program should play a chess game with another player based on
//the board
/** some information about the program
* functions : -1) prtboard :print the currenct board
             - 2) init     :initilize the board to the first state
             - 3) score    :return the difference in score of the board .output>0 means white winning
               output<0 means black is winning
             - 4) turn     :return whose turn it is now
main function 5) best_move:moves the player the best move (return the max-min score that results
                        of this move)
              6) psblemv  :return a pointer and the length to an array which contains all
                        possible moves for all pieces for the player.return null and 0 if it
                        is check mate
              7) check    :return 1 -the king is attacked ,0 -the king it not attacked
              8) pmoves   :gets a piece and return pointer and length to all the moves possible
                        must include transforming pawn,double attack ,fork,sheltering the king
              9) move     :moves a specified piece to a specified place on board
* variables : 1)- board    :global 2 -dimintional array(matrix) 8*8 type int
                        where :white pieces are positive ,black pieces are negative,empty squares
                        are 0.
                        pawn =|1|
                        knight=bishop=|3|
                        rook=|5|
                        queen=|9|
                        king=|1000|
             - 2) pieces   :enum variable for the pieces of the game
             - 3) (turn)   :static int used for the turn function
             - 4) steps    :#define for the number of steps that the program compute
**/
#define STEPS 5
typedef enum {WPAWN=1,BPAWN=-1,WKNIGHT=4,BKNIGHT=-4,WBISHOP=3,BBISHOP=-3,\
WROOK=5,BROOK=-5,WQUEEN=9,BQUEEN=-9,WKING=100,BKING=-100,SQUARE=0}pieces;
typedef enum{WHITE=1,BLACK=-1}player;
 typedef struct move
 {
 int inr,inc,outr,outc,pchange;//pchange if the pawn need to change to other pieces according to it's value
 }move;
int board[8][8];
int spccase[8]={-1,-1,0,0,0,0,0,0};//i,j for pawn special moves,tabyete twelh ll2byd wl2swad ,last to for special tie cases
//first to cloning the other player and second for the 50 moves in the last game
//the case where no move it available is else where
void init();
void prtboard();
int options(player,move**);
int turn()
{
static int t=-1;
return t*=-1;
}
int score();
/*######################*/
int main()
{
    move* ex;
 init();
 prtboard();
 printf("poss=%d\n",options(WHITE,&ex));
return 0;
}
/*######################*/
void init()
{
board[0][0]=WROOK;
board[0][7]=WROOK;
board[0][1]=WKNIGHT;
board[0][6]=WKNIGHT;
board[0][2]=WBISHOP;
board[0][5]=WBISHOP;
board[0][3]=WKING;
board[0][4]=WQUEEN;
for(int i=1;i<7;i++)
    {
     for(int j=0;j<8;j++)
       switch(i)
       {
       case 1:board[i][j]=WPAWN;
              break;
       case 6:board[i][j]=BPAWN;
              break;
       default:board[i][j]=SQUARE;
              break;
       }
    }
board[7][0]=BROOK;
board[7][7]=BROOK;
board[7][1]=BKNIGHT;
board[7][6]=BKNIGHT;
board[7][2]=BBISHOP;
board[7][5]=BBISHOP;
board[7][3]=BKING;
board[7][4]=BQUEEN;
}
void prtboard()
{
printf("+-+--+--+--+--+--+--+--+--+\n");
for(int i=7;i>-1;i--)
    {
        printf("|%d|",i+1);
    for(int j=0;j<8;j++)
        {
        switch(board[i][j])
        {
        case 1:printf("wp"); break;
        case -1:printf("bp"); break;
        case 3:printf("wB"); break;
        case -3:printf("bB"); break;
        case 4:printf("wK"); break;
        case -4:printf("bK"); break;
        case 5:printf("wR"); break;
        case -5:printf("bR"); break;
        case 9:printf("WQ"); break;
        case -9:printf("BQ"); break;
        case 100:printf("WK"); break;
        case -100:printf("BK"); break;
        case 0:printf("  "); break;
        }
        printf("|");
        }
    printf("\n+-+--+--+--+--+--+--+--+--+\n");
    }
 printf("|O|A |B |C |D |E |F |G |H |\n");
}
int score()
{
    int scre=0;
for(int i=0;i<8;i++)
for(int j=0;j<8;j++)
    {
     switch(board[i][j])
     {
      case 4:scre+=3; break;
      case -4:scre-=3; break;
      default: scre+=board[i][j];break;
     }
    }
    return scre;
}
int options(player p,move** list)
{
int i,j,len=0,tmp;
if(p==WHITE)
    {
    if(spccase[2]==1)
        len++;
    if(spccase[3]==1)
        len++;
    }
if(p==BLACK)
    {
     if(spccase[4]==1)
        len++;
     if(spccase[5]==1)
        len++;
    }//askes for the tabyeth
for(i=0;i<8;i++)
{
    for(j=0;j<8;j++)
    {
        if(board[i][j]*p<=0)//means it is enemy or empty square
            continue;
        switch(board[i][j])
        {
        case BPAWN:
        case WPAWN: if((i+p)%7==0)//the pawn is on the other side
                        {
                            if(board[(i+p)][j]==SQUARE)
                                 len+=3;//can become queen rook knight bishop
                            if(j<7 &&board[(i+p)][j+1]*p<0)//if the right square is enemy
                                len+=3;
                            if(j>0 &&board[(i+p)][j-1]*p<0)//if the left square is enemy
                                len+=3;
                        }
                    if(i%7!=0 && board[i+p][j]==SQUARE)//can move on square 'forward'
                        len++;
                    if(j<7 && board[i+p][j+1]*p<0)//can eat to the right
                        len++;
                    if(j>0 && board[i+p][j-1]*p<0)//can eat to the left
                        len++;
                    if((i-p)%7==0)//the pawn is on his initial square
                    {
                        if(board[i+p][j]==SQUARE && board[i+2*p][j]==SQUARE)//can move 2 squares
                            len++;
                    }
                    if(2*i-p==7)//if white is on row 4 or black on row 3 for potential special move
                    {
                     if(i+p==spccase[0] && (j-spccase[1]==1 || spccase[1]-j==1))
                        len++;
                    }
                    break;
                    //stoped here
        case BROOK:
        case WROOK: tmp=i-1;
                    while(tmp>-1)//left side
                        {
                        if(board[tmp][j]==SQUARE)
                            {len++; tmp--; continue;}
                        if(board[tmp][j]*p<0)//first enemy
                            len++;
                        break;
                        }
                    tmp=i+1;
                    while(tmp<8)//right side
                        {
                        if(board[tmp][j]==SQUARE)
                            {len++; tmp++; continue;}
                        if(board[tmp][j]*p<0)//first enemy
                            len++;
                        break;
                        }
                    tmp=j-1;
                    while(tmp>-1)//the bottom side of the ROOK
                    {
                    if(board[i][tmp]==SQUARE)
                        {len++; tmp--; continue;}
                    if(board[i][tmp]*p<0)
                        len++;
                    break;
                    }
                    tmp=j+1;
                    while(tmp<8)//the upper side
                    {
                    if(board[i][tmp]==SQUARE)
                        {len++; tmp++; continue;}
                    if(board[i][tmp]*p<0)
                        len++;
                    break;
                    }
                    break;
        case BKNIGHT:
        case WKNIGHT: if(i+2<8)//the two upper squares
                        {
                            if(j+1<8 &&(board[i+2][j+1]*p<=0))//not a friend
                                len++;
                            if(j-1>-1&&(board[i+2][j-1]*p<=0))
                                len++;
                        }
                      if(i-2>-1)//the two bottom squares
                      {
                          if(j+1<8 &&(board[i-2][j+1]*p<=0))//not a friend
                                len++;
                          if(j-1>-1&&(board[i-2][j-1]*p<=0))
                                len++;
                      }
                      if(j+2<8)//the two right squares squares
                        {
                            if(i+1<8 &&(board[i+1][j+2]*p<=0))//not a friend
                                len++;
                            if(i-1>-1&&(board[i-1][j+2]*p<=0))
                                len++;
                        }
                      if(j-2>-1)//the two left squares squares
                        {
                            if(i+1<8 &&(board[i+1][j-2]*p<=0))//not a friend
                                len++;
                            if(i-1>-1&&(board[i-1][j-2]*p<=0))
                                len++;
                        }
                        break;
                        //stoped here
        }
    }
}
return len;
}
