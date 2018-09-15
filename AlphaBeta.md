# CCA-Project
#My code for AlphaBetaAI

import copy
import time
import ConnectFourEngine
import ConnectFourBoard

def other(token):
    if token == ConnectFourBoard.RED:
        return ConnectFourBoard.BLUE
    elif token == ConnectFourBoard.BLUE:
        return ConnectFourBoard.RED
    else:
        return None


# estimate of a field quality
def state_score(game, field):
    # TODO: complete this function
    (score_red, score_blue) = game.board.score(field)
    if game.red_turn:
        return score_red - score_blue
    else:
        return score_blue - score_red


def max_play(game, field, fieldheights, remaining_ply,alpha, beta):
    # TODO: implement Max playing
    moves = []
    value = -100000000000
    #Loop over all columns
    for col in range(0, game.board.width):
        #Only analyse columns that are not full
        if fieldheights[col] < game.board.height:
            #Copy the game state so the AI can mess with it
            newfield = copy.deepcopy(field)
            newheights = copy.deepcopy(fieldheights)
            #Insert a new token
            token = 1 if game.red_turn else 2
            newfield[col][newheights[col]] = token
            newheights[col] += 1
            vv = state_score(game, newfield)
            if remaining_ply <= 0:
                if vv > value:
                    value = vv
                if vv >= beta:
                    return max(moves, key = lambda x: x[1]) #Returns the best move for the player at that step in the tree
                if vv > alpha:
                    alpha = vv
            elif game.win_check(fieldheights = newheights): #If you win using this move, give this move a very high value
                value = 1000
            else:
                (min_move, value) = min_play(game, newfield, newheights, remaining_ply-1, alpha, beta)
            moves += [(col, value)]
    return max(moves, key = lambda x: x[1]) #Returns the best move for the player at that step in the tree

def min_play(game, field, fieldheights, remaining_ply, alpha, beta):
    # TODO: implement Max playing
    moves = []
    value = 1000000000000000
    #Loop over all columns
    for col in range(0, game.board.width):
        #Only analyse columns that are not full
        if (fieldheights[col] < game.board.height):
            #Copy the game state so the AI can mess with it
            newfield = copy.deepcopy(field)
            newheights = copy.deepcopy(fieldheights)
            #Insert a new token
            token = 2 if game.red_turn else 1
            newfield[col][newheights[col]] = token
            newheights[col] += 1
            vv = state_score(game, newfield)
            if remaining_ply <= 0:
                if vv < value:
                    value = vv
                if vv <= alpha:
                    return max(moves, key = lambda x: x[1]) #Returns the best move for the player at that step in the tree
                if vv < beta:
                    beta  = vv
            elif game.win_check(fieldheights=newheights): #If opponent wins using this move, make the move have a very low value
                value = -1000
            else:
                (min_move, value) = max_play(game, newfield, newheights, remaining_ply-1, alpha, beta)
            moves += [(col,value)]
    return min(moves, key = lambda x: x[1]) #Returns the best move for the opponent at this step in the tree
def AIcheck(game, token,):
    # Modify to set a different search depth
    remaining_ply = 1
    start = time.time()
    alpha = -100000000000
    beta = 10000000000
    fieldheights = [game.board.col_height(col) for col in range(game.board.width)]
    (move, value) = max_play(game, game.board.field, fieldheights, remaining_ply, alpha, beta)
    print(time.time() - start)
    return move
