# Gamechanger
Temple run
import javafx.animation.AnimationTimer;
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.input.KeyEvent;
import javafx.scene.layout.Pane;
import javafx.scene.paint.Color;
import javafx.scene.shape.Rectangle;
import javafx.stage.Stage;

public class TempleRunGame extends Application {

    private static final double WIDTH = 800;
    private static final double HEIGHT = 600;
    
    private Rectangle player;
    private double playerSpeed = 4;
    private double playerYSpeed = 0;
    private double gravity = 0.5;
    private boolean isJumping = false;

    private Rectangle obstacle;
    private double obstacleSpeed = 3;

    public static void main(String[] args) {
        launch(args);
    }

    @Override
    public void start(Stage primaryStage) {
        // Create the player
        player = new Rectangle(50, 50, Color.BLUE);
        player.setX(100);
        player.setY(HEIGHT - 100);

        // Create an obstacle
        obstacle = new Rectangle(50, 50, Color.RED);
        obstacle.setX(WIDTH);
        obstacle.setY(HEIGHT - 100);

        // Create the game layout
        Pane gamePane = new Pane();
        gamePane.getChildren().addAll(player, obstacle);

        // Handle keyboard input
        gamePane.setOnKeyPressed(event -> handleKeyPress(event));

        // Set up the game scene
        Scene scene = new Scene(gamePane, WIDTH, HEIGHT);
        scene.setFill(Color.LIGHTBLUE);
        scene.setOnKeyPressed(event -> handleKeyPress(event));
        
        // Make sure the game is focused to receive key events
        scene.setFocusTraversable(true);
        
        primaryStage.setTitle("Temple Run - Simplified");
        primaryStage.setScene(scene);
        primaryStage.show();

        // Start the game loop
        startGameLoop(gamePane);
    }

    // Handle player movement (left, right, jump, etc.)
    private void handleKeyPress(KeyEvent event) {
        switch (event.getCode()) {
            case LEFT:
                if (player.getX() > 0) {
                    player.setX(player.getX() - playerSpeed);
                }
                break;
            case RIGHT:
                if (player.getX() < WIDTH - player.getWidth()) {
                    player.setX(player.getX() + playerSpeed);
                }
                break;
            case SPACE:
                if (!isJumping) {
                    isJumping = true;
                    playerYSpeed = -10; // Jump speed
                }
                break;
        }
    }

    // The game loop, continuously updates the game state
    private void startGameLoop(Pane gamePane) {
        AnimationTimer gameLoop = new AnimationTimer() {
            @Override
            public void handle(long now) {
                // Move the obstacle
                obstacle.setX(obstacle.getX() - obstacleSpeed);
                if (obstacle.getX() < 0) {
                    obstacle.setX(WIDTH); // Reset the obstacle when it goes off-screen
                }

                // Update player position based on gravity and jumping
                if (isJumping) {
                    player.setY(player.getY() + playerYSpeed);
                    playerYSpeed += gravity; // Apply gravity to downward movement
                    if (player.getY() >= HEIGHT - 100) { // Player hits the ground
                        player.setY(HEIGHT - 100);
                        isJumping = false;
                        playerYSpeed = 0;
                    }
                }

                // Check for collision with obstacle
                if (player.getBoundsInParent().intersects(obstacle.getBoundsInParent())) {
                    stop(); // End the game if player hits the obstacle
                    System.out.println("Game Over!");
                }
            }
        };

        gameLoop.start();
    }
}
