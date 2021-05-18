---
title: 2021 Spring Challenge
last_updated: May 17, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: 2021_Spring_Challenge.html
summary: 


---

## [2021 Spring Challenge](https://www.codingame.com/contests/spring-challenge-2021)

> All rights reserved Codingame.com 

![2021 Spring Challenge](/images/2021_spring_challenge.png)


### First strategy : if-else

What I first tried was with a lot of if-else operations. 
I still managed to get quite okay score, but it wasn't good enough, and I didn't have many conditions that I could come up with, so I changed strategy.

### Second Strategy : heuristic

Since the game gives you possible actions, I decided to weight them and do the best action at the moment. 
It worked really well at first, but it took a lot of time to balance weights between actions. I ended up kept using this strategy till the end, but it wasn't the best tactics on the game.

At the last moment, I realized many of algorithm have similarity when it comes to seeding, so I was trying to predict where the opponent would put seed and prevent it. It wasn't a great succeed, but I'm sure it could've if I did a good job on weight balancing.

My strategy was to evaluate next possible actions. But I should've evaluate possible states. Mine was only able to see what seems to be the best action for the next round, but with states, you can see more than 1 round, as far as memory allows. 

So people encourage me to use best first search, or beam search. It seems to work the best especially this contest, but I didn't have enough time to implement it.



```c++
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <tuple>
#include <cstring>

using namespace std;

class Cell {
public:
    Cell () {
        neighbors.resize(6);
    }

    void input() {
        cin >> cell_index >> richness;
        for (auto& n: neighbors) {
            cin >> n;
        }
        tree_size = -1;
    }
    int cell_index;
    int richness;
    int tree_size;
    int whoes;
    vector<int> neighbors;
};

class Tree {
public:
    Tree () = default;
    Tree (int cell_index, int size, bool is_mine, bool is_dormant) :
        cell_index{cell_index}, size{size}, is_mine{is_mine}, is_dormant{is_dormant} {}
    void input() {
        cin >> cell_index >> size >> is_mine >> is_dormant;
    }
    int cell_index;
    int size;
    bool is_mine;
    bool is_dormant;
};

class Game {
private:
        int day = 0;
        int nutrients = 0;
        vector<Cell> board;
        vector<double> opp_board;
        vector<Tree> trees;
        vector<tuple<string,int,int, int, double>> possible_actions;
        vector<tuple<string,int,int, int, double>> opp_possible_actions;
        int mySun;
        int oppSun;
        int score;
        int oppScore;
        int oppIsWaiting;
        int left_round;
        int treeCnt[4];
        int opptreeCnt[4];
        vector<vector<vector<int>>> shade_spots;

public:
    void inputInitData() {
        int numberOfCells;
        cin >> numberOfCells;
        for (int i = 0; i < numberOfCells; i++) {
            Cell cell;
            cell.input();
            board.push_back(cell);
        }

        int dir[6][2] = {{1,5},{0,2},{1,3},{2,4},{3,5},{0,4}};
        shade_spots = vector<vector<vector<int>>>(37, vector<vector<int>>(4, vector<int>()));
        for(int idx = 0; idx < 37; idx++){
            for(int dir = 0; dir < 6; dir++){
                int cellIdx = board[idx].neighbors[dir];
                if(cellIdx == -1) continue;
                shade_spots[idx][1].push_back(cellIdx);
                
                cellIdx = board[cellIdx].neighbors[dir];
                if(cellIdx == -1) continue;
                shade_spots[idx][2].push_back(cellIdx);
                
                cellIdx = board[cellIdx].neighbors[dir];
                if(cellIdx == -1) continue;
                shade_spots[idx][3].push_back(cellIdx);

            }
        }

        // for(int idx = 0; idx < 37; idx++){
        //     cerr << idx << ':' << endl;
        //     for(int j = 0; j < 4; j++){
        //         cerr << "       " << j << ":" << endl;
        //         for(int k = 0; k < shade_spots[idx][j].size(); k++){
        //             cerr << shade_spots[idx][j][k] << ", ";
        //         }
        //         cerr << endl;
        //     }
        //     cerr << endl;
        // }
    }

    void inputInfo() {
        // input game info
        cin >> day;
        cin >> nutrients;
        cin >> mySun >> score;
        cin >> oppSun >> oppScore >> oppIsWaiting;
        
        left_round = 24 - day;
        
        // input trees info
        trees.clear();
        for(auto& cell : board) {
            cell.tree_size = -1;
            cell.whoes = 0;
        }

        int numberOfTrees;
        cin >> numberOfTrees;
        memset(treeCnt, 0, sizeof treeCnt);
        memset(opptreeCnt, 0, sizeof treeCnt);
        opp_board = vector<double>(37, 0);
        for (int i = 0; i < numberOfTrees; i++) {
            Tree tree;
            tree.input();
            trees.push_back(tree);
            // cerr<< tree.cell_index << ','<<tree.is_mine<<endl;

            if(tree.is_mine)treeCnt[tree.size]++;
            else opptreeCnt[tree.size]++;
            board[tree.cell_index].tree_size = tree.size;
            board[tree.cell_index].whoes = tree.is_mine? 1 : -1;
        }
        // for(auto cell : board) cerr<<cell.tree_size << endl;;
        // for(int i = 0; i < 4; i++){
        //     cerr << treeCnt[i] << endl;
        // }

        // input possible actions
        possible_actions.clear();
        int numberOfPossibleMoves;
        cin >> numberOfPossibleMoves;
        for (int i = 0; i < numberOfPossibleMoves; i++) {
            string type;
            int arg1 = -1;
            int arg2 = -1;
            cin >> type;
            
            if (type == "WAIT") {
                possible_actions.push_back(make_tuple(type, arg1,arg2, 0, 0));
            } else if (type == "COMPLETE") {
                cin >> arg2;
                possible_actions.push_back(make_tuple(type, arg1,arg2, 0, 0));
            }
            else if (type == "GROW") {
                cin >> arg1;
                possible_actions.push_back(make_tuple(type, arg1,arg2, 0, 0));
            }
            else if (type == "SEED") {
                cin >> arg1;
                cin >> arg2;
                possible_actions.push_back(make_tuple(type, arg1,arg2, 0, 0));
            }
        }

        cerr<<"possible act : not by me : "<<possible_actions.size()<<endl;
        // possible_to_string();
        // Growing a seed into a size 1 tree costs 1 sun point + the number of size 1 trees you already own.
        // Growing a size 1 tree into a size 2 tree costs 3 sun points + the number of size 2 trees you already own.
        // Growing a size 2 tree into a size 3 tree costs 7 sun points + the number of size 3 trees you already own.
        
        possible_actions.clear();
        possible_actions.push_back(make_tuple("WAIT", 0,0, 0, 0));
        for(auto tree : trees){
            if(tree.is_mine){
                if(tree.is_dormant) continue;

                if(tree.size == 0 && mySun >= 1 + treeCnt[1]){
                    possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 1 + treeCnt[1], 0));
                } else if(tree.size == 1 && mySun >= 3 + treeCnt[2]){
                    possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 3 + treeCnt[2], 0));
                } else if(tree.size == 2 && mySun >= 7 + treeCnt[3]){
                    possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 7 + treeCnt[3], 0));
                } else if(tree.size == 3 && mySun >= 4){
                    possible_actions.push_back(make_tuple("COMPLETE", tree.cell_index,-1, 4, 0));
                }
                if(mySun < treeCnt[0]) continue;
                possible_seed(tree);
            } else {
                if(tree.is_dormant) continue;

                if(tree.size == 0 && oppSun >= 1 + opptreeCnt[1]){
                    opp_possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 1 + opptreeCnt[1], 0));
                } else if(tree.size == 1 && oppSun >= 3 + opptreeCnt[2]){
                    opp_possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 3 + opptreeCnt[2], 0));
                } else if(tree.size == 2 && oppSun >= 7 + opptreeCnt[3]){
                    opp_possible_actions.push_back(make_tuple("GROW", tree.cell_index,-1, 7 + opptreeCnt[3], 0));
                } else if(tree.size == 3 && oppSun >= 4){
                    opp_possible_actions.push_back(make_tuple("COMPLETE", tree.cell_index,-1, 4, 0));
                }
                if(oppSun < opptreeCnt[0]) continue;
                possible_seed(tree);
            }
        }

        cerr<<"possible act :: "<<possible_actions.size()<<endl;
        // possible_to_string();
    }
    
    void possible_seed(Tree tree) {
        // cerr<<tree.cell_index<<endl;
        if(tree.size == 0) return;
        vector<int> res;
        dfs(tree.cell_index, tree.size, res);
    
        sort(res.begin(), res.end());
        res.erase(unique(res.begin(), res.end()), res.end());
        // for(auto i : res){
        //     cerr << i << ',';
        // }
        // cerr<<endl;

        for(auto to : res){
            if(tree.cell_index == to) continue;

            // cerr << tree.cell_index << "->" << to << endl;
            if(tree.is_mine){
                possible_actions.push_back(make_tuple("SEED", tree.cell_index, to, treeCnt[0], 0));
            } else {
                opp_possible_actions.push_back(make_tuple("SEED", tree.cell_index, to, opptreeCnt[0], 0));
            }
        }
    }

    void dfs(int cur_cell_idx, int curCnt, vector<int>& curV){
        // cerr << cur_cell_idx << ':' << board[cur_cell_idx].richness<< ','<<board[cur_cell_idx].tree_size << endl;
        if(board[cur_cell_idx].richness != 0 && board[cur_cell_idx].tree_size == -1) curV.push_back(cur_cell_idx);
        if(curCnt == 0) return;

        for(auto cell : board[cur_cell_idx].neighbors){
            if(cell == -1) continue;

            dfs(cell, curCnt-1, curV);
        }
    }

    void weight_possible_actions(){
        //  상대방 행동 weight
        for(auto& act : opp_possible_actions){
            string type = get<0>(act);
            if(type == "WAIT"){
            	double temp = 8 - (mySun/2);
                get<4>(act) = temp;
            } else if(type == "GROW"){
                // get<4>(act) = get_grow_weight(act);
            } else if(type == "SEED"){
                get<4>(act) = get_opp_seed_weight(act);
            } else if(type == "COMPLETE"){
                // get<4>(act) = get_complete_weight(act);
            }
        }

        sort(opp_possible_actions.begin(), opp_possible_actions.end(), [](tuple<string,int,int, int, double> a, tuple<string,int,int, int, double> b){
            // cerr<<get<4>(a) << ">=" << get<4>(b) << endl;
            // if(get<0>(a) == "SEED" && get<0>(b) == "SEED" && get<4>(a) == get<4>(b)){
            //     return get<1>(a) > get<1>(b);
            // }
            return get<4>(a) > get<4>(b);
        });

        // opp_possible_to_string();

        //  상대방 seed 가능 위치 예측 후 weight 추가
        for(auto act : opp_possible_actions){
            // if(get<4>(act) < 8 - (oppSun/2)) continue;
            if(get<0>(act) == "SEED"){
                opp_board[get<2>(act)] = get<4>(act)/8.0;
            }
        }

        // for(int i = 0; i < 37; i++){
        //     cerr << i << ':' << opp_board[i] << endl;
        // }

        //  내 행동 weight
        for(auto& act : possible_actions){
            string type = get<0>(act);
            if(type == "WAIT"){
            	double temp = 8 - (mySun/2);
                get<4>(act) = temp;
            } else if(type == "GROW"){
                get<4>(act) = get_grow_weight(act);
            } else if(type == "SEED"){
                get<4>(act) = get_seed_weight(act);
            } else if(type == "COMPLETE"){
                get<4>(act) = get_complete_weight(act);
            }
        }

        sort(possible_actions.begin(), possible_actions.end(), [](tuple<string,int,int, int, double> a, tuple<string,int,int, int, double> b){
            // cerr<<get<4>(a) << ">=" << get<4>(b) << endl;
            if(get<0>(a) == "SEED" && get<0>(b) == "SEED" && get<4>(a) == get<4>(b)){
                return get<1>(a) > get<1>(b);
            }
            return get<4>(a) > get<4>(b);
        });

        // sort(opp_possible_actions.begin(), opp_possible_actions.end(), [](tuple<string,int,int, int, double> a, tuple<string,int,int, int, double> b){
        //     // cerr<<get<4>(a) << ">=" << get<4>(b) << endl;
        //     // if(get<0>(a) == "SEED" && get<0>(b) == "SEED" && get<4>(a) == get<4>(b)){
        //     //     return get<1>(a) > get<1>(b);
        //     // }
        //     return get<4>(a) > get<4>(b);
        // });
        

    }

    double get_opp_seed_weight(tuple<string,int,int, int, double> act){
        int from = get<1>(act);
        int to = get<2>(act);
        int sun_needed = get<3>(act);

        double possibility_of_finishing_the_seed = left_round / 9 > 1 ? 1 : left_round / 9.0;
        double bonus = get_bonus(board[to].richness) * 2.5;
        double when_it_s_done = (nutrients + bonus) * possibility_of_finishing_the_seed;

        double not_too_close = 1;
        if(from != -1) {
            for(int i = 0; i < 6; i++){
                if(board[to].neighbors[i] == from){
                    not_too_close = 5;
                    break;
                }
            }
        }

        double surrounding_trees = 1;
        //  shade_spots[37][4][n]
        for(int i = 1; i < 4; i++){
            for(int j = 0; j < shade_spots[to][i].size(); j++){
                Cell temp_cell = board[shade_spots[to][i][j]];
                //  100% shadow
                if(temp_cell.tree_size >= i) {
                    surrounding_trees += temp_cell.whoes == 0 ? 0.5 : 0.3;
                }
                //  possible shadow
                else if(temp_cell.tree_size != -1){
                    surrounding_trees += temp_cell.whoes == 0 ? 0.3 : 0.1;
                }
            }
        }

        double current_weight = when_it_s_done - (sun_needed * 22);
        current_weight = current_weight > 0 ? current_weight / surrounding_trees : current_weight * surrounding_trees;
        current_weight /= not_too_close;
        return current_weight;
    }

    int get_bonus(int richness){
        int temp;
        switch(richness){
            case 3 : temp = 4;
            break;
            case 2 : temp = 2;
            break;
            default : temp = 0;
            break;
        }
        return temp;
    }

    double get_complete_weight(tuple<string,int,int, int, double> act){
        int to = get<1>(act);

        // get_seed_weight 는 get<2>가 to이다(수정필요)
        // double possibility_of_regrowing = get_seed_weight(act) / 2;
        double close_to_end = left_round <= (treeCnt[3] / 3.0) + 2 ? 200.0/left_round : ((treeCnt[3] / 3.0) + 2)/left_round;
        // cerr << 10/left_round << ',' << 10.0/left_round;
        if(left_round <= 4) close_to_end = 999;

        double edge_bonus = 0;
        if(19 <= to && to <= 36){
            edge_bonus = 5;
        }

        double tree_weight = (treeCnt[1] / 5.0) + (treeCnt[2] / 4.0) + (treeCnt[3]);
        double too_many_trees = treeCnt[3] >= 4 ? 2 : 1;

        double total_sum = ((nutrients + get_bonus(board[to].richness)) * 2.3) - ((30.0 / tree_weight)/(24.0/left_round));
        if(total_sum < 0) total_sum = 1;
        total_sum *= close_to_end;
        total_sum *= too_many_trees;
        total_sum -= edge_bonus;
        return total_sum;
    }

    double get_seed_weight(tuple<string,int,int, int, double> act){
        int from = get<1>(act);
        int to = get<2>(act);
        int sun_needed = get<3>(act);

        double possibility_of_finishing_the_seed = left_round / 9 > 1 ? 1 : left_round / 9.0;
        double bonus = get_bonus(board[to].richness) * 2.5;
        double when_it_s_done = (nutrients + bonus + opp_board[to]) * possibility_of_finishing_the_seed;

        double not_too_close = 1;
        if(from != -1) {
            for(int i = 0; i < 6; i++){
                if(board[to].neighbors[i] == from){
                    not_too_close = 5;
                    break;
                }
            }
        }

        double surrounding_trees = 1;
        //  shade_spots[37][4][n]
        for(int i = 1; i < 4; i++){
            for(int j = 0; j < shade_spots[to][i].size(); j++){
                Cell temp_cell = board[shade_spots[to][i][j]];
                //  100% shadow
                if(temp_cell.tree_size >= i) {
                    surrounding_trees += temp_cell.whoes == 1 ? 0.5 : 0.3;
                }
                //  possible shadow
                else if(temp_cell.tree_size != -1){
                    surrounding_trees += temp_cell.whoes == 1 ? 0.3 : 0.1;
                }
            }
        }

        double current_weight = when_it_s_done - (sun_needed * 22);
        current_weight = current_weight > 0 ? current_weight / surrounding_trees : current_weight * surrounding_trees;
        current_weight /= not_too_close;
        return current_weight;
    }

    double get_grow_weight(tuple<string,int,int, int, double> act){
        int to = get<1>(act);
        int tree_size = board[to].tree_size;
        int cell_richness = board[to].richness;

        // double grow_size_2_first = 0;
//         if(left_round <= 20) grow_size_2_first = 20/left_round;

        double bonus = 0;
        switch(cell_richness){
            case 3: bonus = 10;
            break;
            case 2: bonus = 5;
            break;
            default: bonus = 0;
            break;
        }

        double score_per_tree_size = 0;
        switch(tree_size){
            case 2: score_per_tree_size = (nutrients * 1.5) - ((7 + treeCnt[3])/3.0);
            break;
            case 1: score_per_tree_size = (nutrients * 1.2) - ((3 + treeCnt[2])/3.0);
            break;
            default: score_per_tree_size = (nutrients * 1.0)  - ((1 + treeCnt[1])/3.0);
            break;
        }
        return ((score_per_tree_size * 2) + bonus) / 2.0;
    }

    void possible_to_string() {
        for(auto act : possible_actions){
            cerr << get<0>(act) + " " + to_string(get<1>(act)) + " " + to_string(get<2>(act)) + " " + to_string(get<3>(act)) + " " + to_string(get<4>(act)) << endl;
        }
    }

    void opp_possible_to_string() {
        cerr << "opp_possible_to_string : " << endl;
        for(auto act : opp_possible_actions){
            cerr << get<0>(act) + " " + to_string(get<1>(act)) + " " + to_string(get<2>(act)) + " " + to_string(get<3>(act)) + " " + to_string(get<4>(act)) << endl;
        }
    }

    // TODO: Please implement the algorithm in this function
    string compute_next_action() {
        string action = get<0>(possible_actions[0]) + " " + to_string(get<1>(possible_actions[0])) + " " + to_string(get<2>(possible_actions[0])) + " " + get<0>(possible_actions[0]); // default
        if(get<0>(possible_actions[0]) == "") action = "WAIT";

        return action;
    }
};

int main()
{
    Game game;
    game.inputInitData();

    while (true) {
        game.inputInfo();
        game.weight_possible_actions();
        game.possible_to_string();
        
        cout << game.compute_next_action() << endl;
    }
}
```



### Other Strategies

You can check what others implemented it [here](https://www.codingame.com/forum/t/spring-challenge-2021-feedbacks-strategies/190849/17)



### Conclusion

It was little disappointed that I didn't get to the legend league, but I know I did my best at the circumstance. 

I've learned, to evaluate either states or actions, you have to have as many information as you can, and that always helps. But it's also important how to weight them. Was fun!



Pros:

1. I've learned many new things while working on it. 
   1. algorithms
   2. brutaltester
2. Realized that sometime it's efficient to start from the beginning.
   1. I originally started with JS but changed it to C++. I made many mistakes, but it gave me better result at the end.
   2. I could've implemented the beam search and see if it makes good progress. 
