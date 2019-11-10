#include <iostream>
#include <fstream>
#include <algorithm>
#include <cmath>
#include <stack>
#include <queue>
#include <stdlib.h>
#include <stdio.h>

using namespace std;

int m, n, B;

struct block{
    int exist;
    int x;
    int y;
    block* up;
    block* down;
    block* left;
    block* right;
};

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

block* generate_map(int m, int n){
    block* row_1 = generate_row(n, 0);
    block* b_11 = row_1;
    for(int i = 1; i<m; i++){
        block* row_2 = generate_row(n, i);
        block* row_11 = row_1;
        block* row_21 = row_2;
        for(int j = 0; j<n; j++){
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

block* create_map(block* Map){
    block* head = Map;
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
    return Map;
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
    block* Map = generate_map(m, n);
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
