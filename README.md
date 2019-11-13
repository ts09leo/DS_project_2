#include <iostream>
#include <fstream>
#include <algorithm>
#include <cmath>
#include <stack>
#include <queue>
#include <stdlib.h>
#include <stdio.h>
#include <vector>

using namespace std;

struct block{
    int exist;
    int x;
    int y;
    block* up;
    block* down;
    block* left;
    block* right;
    block* prev = NULL;
    block* next = NULL;
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
                    now->exist = -1;
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

void reconstruct_map(block* now, block* prev, int dis, int check){
    if(now && now->exist >= 0){
        cout<<"("<<now->y<<" "<<now->x<<")";
        if(now->exist == 0 && now->visit == true){
            return;
        }
        else if(now->exist == 0){
            now->exist = dis;
            now->visit = true;
            now->prev = prev;
            now->prev->next = now;
        }
        else if(now->exist > 0){
            if(now->exist > dis){
                now->exist = dis;
                now->prev = prev;
                now->prev->next = now;
            }
            else
                return;
        }
        switch(check){
        case 0:
            {
                reconstruct_map(now->up, now, dis+1, 1);
                reconstruct_map(now->down, now, dis+1, 2);
                reconstruct_map(now->right, now, dis+1, 3);
                reconstruct_map(now->left, now, dis+1, 4);
                break;
            }
        case 1:
            {
                reconstruct_map(now->up, now, dis+1, 1);
                reconstruct_map(now->right, now, dis+1, 3);
                reconstruct_map(now->left, now, dis+1, 4);
                break;
            }
        case 2:
            {
                reconstruct_map(now->down, now, dis+1, 2);
                reconstruct_map(now->right, now, dis+1, 3);
                reconstruct_map(now->left, now, dis+1, 4);
                break;
            }
        case 3:
            {
                reconstruct_map(now->up, now, dis+1, 1);
                reconstruct_map(now->down, now, dis+1, 2);
                reconstruct_map(now->right, now, dis+1, 3);
                break;
            }
        case 4:
            {
                reconstruct_map(now->up, now, dis+1, 1);
                reconstruct_map(now->down, now, dis+1, 2);
                reconstruct_map(now->left, now, dis+1, 4);
                break;
            }
        }
    }
}

bool cmp_1(const block* a, const block* b){
    return a->exist >= b->exist;
}

void walk(block* to_go){
    int n = to_go->exist;
    block* print[n];
    block* now = to_go;
    for(int i = 0; i < n; i++){
        print[i] = now;
        now->visit = true;
        now = now->prev;
    }
    for(int i = n-1; i >= 0 ; i--){
        cout<<print[i]->y<<" "<<print[i]->x<<endl;
    }
    now = to_go;
    for(int i = 1; i < n; i++){
        cout<<now->y<<" "<<now->x<<endl;
        now = now->prev;
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
    block* stop = new block;
    stop -> exist = -777;
    stop->down = stop->left = stop->right = stop->up = NULL;
    stop->x = stop->y = -777;
    stop->next = robot;
    stop->prev = NULL;
    reconstruct_map(robot, stop, 0, 0);
    head = Map;
    now = head;
    int num_Block = 0;
    cout<<endl<<endl;
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            //cout<<"("<<now->y<<" "<<now->x<<")";
            if(now->exist<=9 && now->exist >= 0){
                cout<<" ";
            }
            if(now->exist >= 0){
                num_Block += 1;
                now->visit = false;
            }
            cout<<now->exist;
            now = now->right;
        }
        cout<<endl;
        head = head->down;
        now = head;
    }
    cout<<endl<<num_Block<<endl;
    vector<block*> sort_Block;
    //block* sort_Block[num_Block];
    int k = 0;
    head = Map;
    now = head;
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            if(now->exist >= 0){
                sort_Block.push_back(now);
            }
            now = now->right;
        }
        head = head->down;
        now = head;
    }
    for(int i = 0; i < num_Block; i++){
        cout<<"("<<sort_Block[i]->y<<" "<<sort_Block[i]->x<<")";
    }
    sort(sort_Block.begin(), sort_Block.end(), cmp_1);
    //sort(sort_Block.begin(), sort_Block.end(), cmp_2);
    //qsort(sort_Block, num_Block, sizeof(block*), cmp);
    cout<<endl<<"After sorting"<<endl;
    for(int i = 0; i < num_Block; i++){
        cout<<sort_Block[i]->exist<<"("<<sort_Block[i]->y<<" "<<sort_Block[i]->x<<")"<<" ";
    }
    cout<<endl;
    robot->visit = true;
    for(int i = 0; i < num_Block; i++){
        block* going = sort_Block[i];
        cout<<going->exist<<endl;
        if(going->visit == false){
            cout<<robot->y<<" "<<robot->x<<endl;
            walk(going);
            cout<<robot->y<<" "<<robot->x<<endl;
        }
        sort_Block.pop_back();
    }
    for(int i = 0; i < num_Block; i++){
        if(sort_Block[i]->visit == false){
            cout<<sort_Block[i]->y<<" "<<sort_Block[i]->x<<" do not visit!!!"<<endl;
            break;
        }
        else if(i == num_Block-1){
            cout<<"true"<<endl;
        }
    }
    return 0;
}
