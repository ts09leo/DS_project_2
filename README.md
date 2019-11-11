#include <iostream>
#include <fstream>
#include <algorithm>
#include <cmath>
#include <stack>
#include <queue>
#include <stdlib.h>
#include <stdio.h>

using namespace std;

struct block{
    int exist;
    int x;
    int y;
    block* up;
    block* down;
    block* left;
    block* right;
    bool visit = false;
};

block* Map;
int m, n, B;

block* generate_row(int n, int y){
    int j;
    block* block_1 = new block;
    block* first = block_1;
    block_1->left = NULL;
    block_1->down = NULL;
    block_1->up = NULL;
    block_1->exist = 0;
    block_1->x = 0;
    block_1->y = y;
    for(j=2; j<=n; j++){
        block* block_2 = new block;
        block_1->right = block_2;
        block_2->left = block_1;
        block_2->exist = 0;
        block_2->x = j-1;
        block_2->y = y;
        block_2->up = NULL;
        block_2->down =NULL;
        block_1 = block_2;
    }
    block_1->right = NULL;
    return first;
}

block* generate_map(int M, int N){
    block* row_1 = generate_row(N, 0);
    block* b_11 = row_1;
    for(int i = 1; i<M; i++){
        block* row_2 = generate_row(N, i);
        block* row_11 = row_1;
        block* row_21 = row_2;
        for(int j = 0; j<N; j++){
            row_1->down = row_2;
            row_2->up = row_1;
            if(row_1->right != NULL){
                row_1 = row_1->right;
                row_2 = row_2->right;
            }
            else{
                row_1 = row_21;
                break;
            }
        }
    }
    return b_11;
}

block* create_map(block* MAP){
    block* head = MAP;
    block* now = head;
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            char temp;
            cin>>temp;
            switch(temp){
            case 'R':
                {
                    now->exist = -7;
                    break;
                }
            case '1':
                {
                    now->exist = 1;
                    break;
                }
            case '0':
                {
                    now->exist = 0;
                    break;
                }
            }
            now = now->right;
        }
        head = head->down;
        now = head;
    }
    return MAP;
}

void reconstruct_map(block* now, int dis){
    if(now){
        if(now->exist == 0 && now->visit == false){
            now->exist = dis;
            now->visit = true;
            reconstruct_map(now->up, dis+1);
            reconstruct_map(now->down, dis+1);
            reconstruct_map(now->right, dis+1);
            reconstruct_map(now->left, dis+1);
        }
        if(now->exist == 1 && now->visit == false){
            now->visit = true;
            now->exist = -1;
        }
    }
}

int main()
{
    /*fstream file("floor.data");
    if(!file){
        cout<<"can't read file"<<endl;
        return 1;
    }
    file>>m>>n>>B;*/
    cin>>m>>n>>B;
    cout<<"m = "<<m<<" n = "<<n<<" B = "<<B<<endl;
    Map = generate_map(m, n);
    block* head = Map;
    block* now = head;
    /*for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            //cout<<"("<<now->y<<" "<<now->x<<")";
            cout<<now->exist;
            now = now->right;
        }
        cout<<endl;
        head = head->down;
        now = head;
    }
    head = Map;
    now = head;*/
    Map = create_map(Map);
    block* robot;
    for(int i = 0; i < m; i++){
        int stop = 0;
        if(stop == 1)
            break;
        for(int j = 0; j < n; j++){
            if(now->exist == -7){
                robot = now;
                stop = 1;
                break;
            }
            now = now->right;
        }
        head = head->down;
        now = head;
    }
    robot->exist = 0;
    reconstruct_map(robot, 0);
    head = Map;
    now = head;
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            //cout<<"("<<now->y<<" "<<now->x<<")";
            cout<<now->exist;
            now = now->right;
        }
        cout<<endl;
        head = head->down;
        now = head;
    }
    return 0;
}
