import curses
import random
import time

def main(stdscr):
    curses.curs_set(0)  # Hide the cursor
    stdscr.nodelay(1)   # Non-blocking input
    stdscr.timeout(100) # Control game speed

    height, width = stdscr.getmaxyx()
    player_x = width // 2
    player_y = height - 2
    obstacles = []
    score = 0

    while True:
        stdscr.clear()

        # Spawn obstacles randomly
        if random.randint(1, 10) > 7:
            obstacles.append([0, random.randint(1, width - 2)])

        # Move obstacles down
        new_obstacles = []
        for obs in obstacles:
            obs[0] += 1
            if obs[0] < height - 1:
                new_obstacles.append(obs)
        
        obstacles = new_obstacles

        # Draw obstacles
        for obs in obstacles:
            stdscr.addch(obs[0], obs[1], '#')

        # Draw player
        stdscr.addch(player_y, player_x, '@')

        # Check collision
        for obs in obstacles:
            if obs[0] == player_y and obs[1] == player_x:
                stdscr.addstr(height // 2, width // 3, "GAME OVER!", curses.A_BOLD)
                stdscr.addstr(height // 2 + 1, width // 3, f"Score: {score}")
                stdscr.refresh()
                time.sleep(2)
                return

        # Get user input
        key = stdscr.getch()
        if key == curses.KEY_LEFT and player_x > 1:
            player_x -= 1
        elif key == curses.KEY_RIGHT and player_x < width - 2:
            player_x += 1
        elif key == 27:  # Escape key to quit
            return

        score += 1
        stdscr.addstr(0, 2, f"Score: {score}")

        stdscr.refresh()

# Run the game
curses.wrapper(main)