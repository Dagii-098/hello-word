import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.util.Random;

public class SnakeGame extends JPanel implements ActionListener {
    private final int WIDTH = 800, HEIGHT = 600;
    private final int DOT_SIZE = 10;
    private final int ALL_DOTS = (WIDTH * HEIGHT) / (DOT_SIZE * DOT_SIZE);
    private final int[] x = new int[ALL_DOTS];
    private final int[] y = new int[ALL_DOTS];
    private int dots;
    private int apple_x, apple_y;
    private char direction;
    private boolean running;
    private Timer timer;

    public SnakeGame() {
        setBackground(Color.black);
        setFocusable(true);
        setPreferredSize(new Dimension(WIDTH, HEIGHT));
        addKeyListener(new KeyAdapter() {
            @Override
            public void keyPressed(KeyEvent e) {
                switch (e.getKeyCode()) {
                    case KeyEvent.VK_LEFT:
                        if (direction != 'R') direction = 'L';
                        break;
                    case KeyEvent.VK_RIGHT:
                        if (direction != 'L') direction = 'R';
                        break;
                    case KeyEvent.VK_UP:
                        if (direction != 'D') direction = 'U';
                        break;
                    case KeyEvent.VK_DOWN:
                        if (direction != 'U') direction = 'D';
                        break;
                }
            }
        });
        startGame();
    }

    private void startGame() {
        running = true;
        dots = 3;
        direction = 'R';
        for (int i = 0; i < dots; i++) {
            x[i] = 50 - i * DOT_SIZE;
            y[i] = 50;
        }
        spawnApple();
        timer = new Timer(100, this);
        timer.start();
    }

    private void spawnApple() {
        apple_x = new Random().nextInt(WIDTH / DOT_SIZE) * DOT_SIZE;
        apple_y = new Random().nextInt(HEIGHT / DOT_SIZE) * DOT_SIZE;
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        if (running) {
            g.setColor(Color.red);
            g.fillRect(apple_x, apple_y, DOT_SIZE, DOT_SIZE);
            for (int i = 0; i < dots; i++) {
                g.setColor(i == 0 ? Color.green : Color.white);
                g.fillRect(x[i], y[i], DOT_SIZE, DOT_SIZE);
            }
            Toolkit.getDefaultToolkit().sync();
        } else {
            showGameOver(g);
        }
    }

    private void showGameOver(Graphics g) {
        g.setColor(Color.white);
        g.setFont(new Font("Arial", Font.BOLD, 14));
        g.drawString("Game Over", WIDTH / 2 - 30, HEIGHT / 2);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (running) {
            move();
            checkApple();
            checkCollision();
            repaint();
        }
    }

    private void move() {
        for (int i = dots; i > 0; i--) {
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }
        switch (direction) {
            case 'L':
                x[0] -= DOT_SIZE;
                break;
            case 'R':
                x[0] += DOT_SIZE;
                break;
            case 'U':
                y[0] -= DOT_SIZE;
                break;
            case 'D':
                y[0] += DOT_SIZE;
                break;
        }
    }

    private void checkApple() {
        if (x[0] == apple_x && y[0] == apple_y) {
            dots++;
            spawnApple();
        }
    }

    private void checkCollision() {
        for (int i = dots; i > 0; i--) {
            if (i > 3 && x[0] == x[i] && y[0] == y[i]) {
                running = false;
            }
        }
        if (x[0] < 0 || x[0] >= WIDTH || y[0] < 0 || y[0] >= HEIGHT) {
            running = false;
        }
        if (!running) {
            timer.stop();
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("Snake Game");
        SnakeGame game = new SnakeGame();
        frame.add(game);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }
}
