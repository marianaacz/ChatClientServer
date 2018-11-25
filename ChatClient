//package sample;
import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;
import javafx.scene.control.TextField;
import javafx.scene.input.KeyCode;
import javafx.scene.input.KeyEvent;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.stage.Stage;
import java.io.IOException;
import java.io.PrintWriter;
import java.net.Socket;
import java.util.Scanner;

public class ChatClient extends Application {
    private static String username = "Anonymous";
    private final static int DEFAULT_PORT = 4688;
    private static int port = DEFAULT_PORT;
    private TextArea chatArea = new TextArea();
    private boolean isConnected = false;
    private ChatClient.myChatListener chatListen;
    private Socket socket;
    private Scanner serverReader;
    private PrintWriter serverWriter;

    private class myChatListener extends Thread{
        private boolean connected;
        public myChatListener(){
            connect();
        }
        public void run(){
            while(connected) {
                String message = this.read();
                if (message != null) {
                    ChatClient.this.updateMyChat(message+"\n");
                }
            }

        }
        String read() {
            return ChatClient.this.serverReader.hasNextLine() ? ChatClient.this.serverReader.nextLine() : null;
        }
        public void disconnect(){
            connected = false;
        }
        public void connect(){
            connected = true;
        }

    }

    private void connect() {
        try {
            socket = new Socket("localhost", port); //create socket
            serverReader = new Scanner(this.socket.getInputStream()); //reads
            serverWriter = new PrintWriter(this.socket.getOutputStream(), true); //writes
            chatListen = new ChatClient.myChatListener(); //listens
            chatListen.start();
            serverWriter.println("Connect " + this.username);
            isConnected=true;
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    private void disconnect() {
        if(isConnected) {
            chatListen.disconnect();
            try {
                socket.close();
            } catch (IOException ex) {
                ex.printStackTrace();
            }
        }
        System.exit(0);
    }

    private void updateSelfChat(String message){

        updateMyChat(username+": "+ message);
    }
    private synchronized void updateMyChat(String message){
        chatArea.appendText(message);
        chatArea.positionCaret(chatArea.getText().length());
    }
    private void sendMessage(String message){

        serverWriter.println(message);
    }


    @Override
    public void start(Stage primaryStage) throws Exception {
        BorderPane root = new BorderPane();
        synchronized (chatArea) {
            chatArea.setWrapText(true);
            //ScrollBar scrollBar = (ScrollBar)messages.lookup(".scroll-bar:vertical");
            //scrollBar.setDisable(true);
            //--this.scrollPane = new JScrollPane(this.messages);
            //charArea.setScrollLeft();
            chatArea.setScrollLeft(chatArea.getText().length());
            chatArea.setEditable(false);
            chatArea.positionCaret(chatArea.getText().length());
        }
    

        TextField input = new TextField();
        input.setPrefWidth(300);
        input.setPrefSize(400, 23);
        input.setOnKeyPressed(new EventHandler<KeyEvent>() {
            @Override
            public void handle(KeyEvent ke) {
                if (ke.getCode().equals(KeyCode.ENTER)) {
                    String message = input.getText();
                    updateSelfChat(message + "\n");
                    sendMessage(message);
                    input.clear();
                }
            }
        });


        Button disconnectb = new Button("Disconnect");
        disconnectb.setOnAction(new EventHandler<ActionEvent>() {
            @Override
            public void handle(ActionEvent e) {
                String message = "Disconnect " + username;
                updateMyChat(message + "\n");
                if (isConnected) {
                    sendMessage(message);
                }
                disconnect();
            }
        });

        HBox inputBar = new HBox(); //add the button and the input to the input bar
        inputBar.setAlignment(Pos.CENTER_LEFT);
        inputBar.getChildren().addAll(input, disconnectb);
        inputBar.setFillHeight(true);
        inputBar.setSpacing(5);

        root.setCenter(chatArea);
        root.setBottom(inputBar);
        primaryStage.setTitle("Chat Client");
        primaryStage.setScene(new Scene(root, 500, 500));
        primaryStage.show();
        this.connect();

    }


    public static void main(String[] args) {
        if(args.length==2){
            username = args[0]; //first argument is the username
            try {
                port = Integer.parseInt(args[1]); //second argument is the port number
            }catch(NumberFormatException e){
                System.out.println(e);
            }
        } else if(args.length==1){
            username = args[0];
        }
        launch(args);
    }
}
