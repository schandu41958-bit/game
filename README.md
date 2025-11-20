# game
game
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.Random;

public class SnakeGame extends JPanel implements ActionListener, KeyListener {

    private int width = 600;
    private int height = 600;

    private int unit = 25;
    private int totalUnits = (width * height) / (unit * unit);

    private final int x[] = new int[totalUnits];
    private final int y[] = new int[totalUnits];

    private int snakeLength = 3;
    private int foodX, foodY;

    private char direction = 'R';
    private boolean running = false;

    private Timer timer;
    private Random random;

    public SnakeGame() {
        random = new Random();
        this.setPreferredSize(new Dimension(width, height));
        this.setBackground(Color.black);
        this.setFocusable(true);
        this.addKeyListener(this);
        startGame();
    }

    public void startGame() {
        spawnFood();
        running = true;
        timer = new Timer(100, this);
        timer.start();
    }

    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        draw(g);
    }

    public void draw(Graphics g) {
        if (running) {
            // Food
            g.setColor(Color.red);
            g.fillOval(foodX, foodY, unit, unit);

            // Snake
            for (int i = 0; i < snakeLength; i++) {
                if (i == 0) {
                    g.setColor(Color.green);
                } else {
                    g.setColor(new Color(45, 180, 0));
                }
                g.fillRect(x[i], y[i], unit, unit);
            }

            // Score
            g.setColor(Color.white);
            g.setFont(new Font("Arial", Font.BOLD, 20));
            g.drawString("Score: " + (snakeLength - 3), 10, 20);

        } else {
            gameOver(g);
        }
    }

    public void spawnFood() {
        foodX = random.nextInt((int)(width / unit)) * unit;
        foodY = random.nextInt((int)(height / unit)) * unit;
    }

    public void move() {
        for (int i = snakeLength; i > 0; i--) {
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }

        switch (direction) {
            case 'U' -> y[0] -= unit;
            case 'D' -> y[0] += unit;
            case 'L' -> x[0] -= unit;
            case 'R' -> x[0] += unit;
        }
    }

    public void checkFood() {
        if (x[0] == foodX && y[0] == foodY) {
            snakeLength++;
            spawnFood();
        }
    }

    public void checkCollision() {
        // Self collision
        for (int i = snakeLength; i > 0; i--) {
            if (x[0] == x[i] && y[0] == y[i]) {
                running = false;
                break;
            }
        }

        // Wall collision
        if (x[0] < 0 || x[0] > width - unit || y[0] < 0 || y[0] > height - unit) {
            running = false;
        }

        if (!running) {
            timer.stop();
        }
    }

    public void gameOver(Graphics g) {
        // Score
        g.setColor(Color.red);
        g.setFont(new Font("Arial", Font.BOLD, 40));
        g.drawString("Game Over!", 200, 250);

        g.setColor(Color.white);
        g.setFont(new Font("Arial", Font.BOLD, 30));
        g.drawString("Score: " + (snakeLength - 3), 240, 300);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            move();
            checkFood();
            checkCollision();
        }
        repaint();
    }

    @Override
    public void keyPressed(KeyEvent e) {
        switch (e.getKeyCode()) {
            case KeyEvent.VK_LEFT -> {
                if (direction != 'R') direction = 'L';
            }
            case KeyEvent.VK_RIGHT -> {
                if (direction != 'L') direction = 'R';
            }
            case KeyEvent.VK_UP -> {
                if (direction != 'D') direction = 'U';
            }
            case KeyEvent.VK_DOWN -> {
                if (direction != 'U') direction = 'D';
            }
        }
    }

    public void keyReleased(KeyEvent e) {}
    public void keyTyped(KeyEvent e) {}

    public static void main(String[] args) {
        JFrame frame = new JFrame("2D Snake Game");
        SnakeGame gamePanel = new SnakeGame();

        frame.add(gamePanel);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
        frame.setLocationRelativeTo(null);
    }
}
