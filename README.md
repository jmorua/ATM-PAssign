# ATM-PAssign
JavaFX program that displays the a screen with a balance and buttons that lets you deposit, withdraw, and cancel
/**
 * File: PAssign07.java
 * @author Jayden morua
 * Created on: Oct 26, 2025
 * Modified on: Oct 30, 2025
 * Description: implementing a key pad into a atm machine
 * git hub: https://github.com/jmorua/hello-world
 */
package PAssign;

import java.util.ArrayList;
import java.util.Arrays;
import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.GridPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class PAssign07 extends Application {

	double balance = 56.90; // starting amount // what my current account looks in the moment of making this lol
	StringBuilder input = new StringBuilder();
	Label lblScreen = new Label("Enter amount:\nbalance: " + balance);
	TextField txtDisplay = new TextField();
	Button btnWithdrawl = new Button("Withdrawl");
	Button btnClear = new Button("Clear");
	Button btnCancel = new Button("Cancel");
	Button btnDeposit = new Button("Deposit");
	double amount = 0;

	@Override
	public void start(Stage primaryStage) {
		// Create keypad from provided class
		KeyPadPane keypad = new KeyPadPane();

		// Configure display
		txtDisplay.setEditable(false);
		txtDisplay.setPrefWidth(200);
		txtDisplay.setAlignment(Pos.CENTER);

		// Layout for control buttons
		HBox controlButtons = new HBox(10, btnWithdrawl, btnClear, btnCancel, btnDeposit);
		controlButtons.setAlignment(Pos.CENTER);

		VBox displayArea = new VBox(10, lblScreen, txtDisplay, controlButtons);
		displayArea.setAlignment(Pos.CENTER);

		BorderPane root = new BorderPane();
		root.setTop(displayArea);
		root.setCenter(keypad);
		root.setPrefSize(300, 225);
		BorderPane.setAlignment(displayArea, Pos.CENTER);
		keypad.setAlignment(Pos.TOP_CENTER);

		// Handle keypad button clicks
		for (int i = 0; i < keypad.listButtons.size(); i++) {
			Button button = keypad.listButtons.get(i);
			button.setOnAction(e -> {
				String val = button.getText();
				if (!val.isEmpty() && Character.isDigit(val.charAt(0))) {
					input.append(val);
					txtDisplay.setText(input.toString());
				}
			});
		}

		// Button actions
		btnWithdrawl.setOnAction(e -> {
			if (input.length() == 0) {
				lblScreen.setText("Please enter an amount.");
				return;
			}

			try {
				double amount = Double.parseDouble(input.toString());
				if (amount <= 0) {
					lblScreen.setText("Enter a valid amount.");
				} else {
					balance -= amount;

					if (balance < 0) {
						lblScreen.setText("brookie");
					} else {
						lblScreen.setText(
								String.format("You withdrew $%.2f\nRemaining balance: $%.2f", amount, balance));
					}
				}
			} catch (NumberFormatException ex) {
				lblScreen.setText("Invalid input. Try again.");
			}

			input.setLength(0);
			txtDisplay.clear();
		});

		btnDeposit.setOnAction(e -> {
			if (input.length() == 0) {
				lblScreen.setText("Please enter an amount.");
				return;
			}

			try {
				double amount = Double.parseDouble(input.toString());
				if (amount <= 0) {
					lblScreen.setText("Enter a valid amount.");
				} else {
					balance += amount;
					lblScreen.setText(String.format(
							"You deposited $%.2f\nRemaining balance: $%.2f%nlet me hold a dollar", amount, balance));
				}
			} catch (NumberFormatException ex) {
				lblScreen.setText("Invalid input. Try again.");
			}

			input.setLength(0);
			txtDisplay.clear();
		});

		btnClear.setOnAction(e -> {
			input.setLength(0);
			txtDisplay.clear();
		});

		btnCancel.setOnAction(e -> {
			input.setLength(0);
			txtDisplay.clear();
			lblScreen.setText("Enter amount:\nbalance: " + balance);
		});
    
		// Setup scene
		Scene scene = new Scene(root);
		primaryStage.setTitle("ATM");
		primaryStage.setScene(scene);
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}

class KeyPadPane extends GridPane {
	// private Buttons
	protected Button btn1 = new Button("1");
	protected Button btn2 = new Button("2");
	protected Button btn3 = new Button("3");
	protected Button btn4 = new Button("4");
	protected Button btn5 = new Button("5");
	protected Button btn6 = new Button("6");
	protected Button btn7 = new Button("7");
	protected Button btn8 = new Button("8");
	protected Button btn9 = new Button("9");
	protected Button btnBlank1 = new Button("  ");
	protected Button btn0 = new Button("0");
	protected Button btnBlank2 = new Button("  ");
	protected Button btnAsterisk = new Button("*");
	protected Button btnPound = new Button("#");

	// collect all buttons
	private Button[] arrButtons = { btn1, btn2, btn3, btn4, btn5, btn6, btn7, btn8, btn9, btnBlank1, btn0, btnBlank2 };
	protected ArrayList<Button> listButtons = new ArrayList<>(Arrays.asList(arrButtons));
	protected ArrayList<Button> copyListButtons;

	/**
	 * Create a default KeyPadPane with numbers in order, includes 2 blanks around 0
	 */
	public KeyPadPane() {
		// counter for ArrayList elements
		int counter = 0;

		// place all buttons in 1-9, blank, 0, blank order, 3 per row
		for (int i = 0; i < listButtons.size() / 3; i++) {
			for (int j = 0; j < 3; j++) {
				this.add(listButtons.get(counter++), j, i);
			}
		}

		// call registerEventHandlers() so buttons all work
		registerEventHandlers();
	}

	/**
	 * Create a KeyPadPane with phone order buttons
	 * 
	 * @param phoneOrder specify that the user wants phone keypad layout of buttons,
	 *                   if they use true here, use default layout
	 */
	public KeyPadPane(boolean phoneOrder) {
		// default to default layout for someone choosing false
		this();

		// now check if they want a phone layout
		if (phoneOrder) {
			// get rid of default layout
			this.getChildren().clear();

			// clone list and replace blanks
			copyListButtons = (ArrayList<Button>) listButtons.clone();
			copyListButtons.set(copyListButtons.size() - 3, btnAsterisk);
			copyListButtons.set(copyListButtons.size() - 1, btnPound);

			// counter start at last numeric button index
			int counter = copyListButtons.size() - 4;

			// place all buttons in 9-1, 3 per row
			for (int i = 0; i < (copyListButtons.size() - 3) / 3; i++) {
				for (int j = 0; j < 3; j++) {
					this.add(copyListButtons.get(counter--), j, i);
				}
			}

			// reset counter
			counter = copyListButtons.size() - 1;
			// manually add the blank, 0, blank order
			for (int i = 2; i >= 0; i--) {
				this.add(copyListButtons.get(counter--), i, 3);
			}

			// re-register so the phone's different (#, *) buttons get event handlers
			registerEventHandlers();
		}
	}

	/**
	 * Register a default (basic) action for all Buttons Extend this class and
	 * override this method if you desire different functionality
	 */
	protected void registerEventHandlers() {
		// check if we are using phone layout
		ArrayList<Button> currList = new ArrayList<Button>();
		if (copyListButtons != null) {
			currList = copyListButtons;
		} else {
			currList = listButtons;
		}

		// set event handlers for all Buttons
		for (int i = 0; i < currList.size(); i++) {
			currList.get(i).setOnAction(new EventHandler<ActionEvent>() {
				public void handle(ActionEvent e) {
					System.out.println("Button was clicked: " + ((Button) e.getSource()).getText());
				}
			});
		}

		// free up memory
		currList = null;
	}
}
