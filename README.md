# deber5

package demo_jdbc;

import java.util.List;

import java.util.stream.Collectors;

import demo_jdbc.models.Circuit;

import demo_jdbc.respositories.CircuitRepository;

import demo_jdbc.models.DriverResult;

import demo_jdbc.respositories.DriverResultRepository;

import demo_jdbc.models.Constructors;

import demo_jdbc.respositories.ConstructorsRepository;

import demo_jdbc.models.Season;

import demo_jdbc.respositories.SeasonRepository;

import javafx.application.Application;

import javafx.collections.FXCollections;

import javafx.collections.ObservableList;

import javafx.scene.Scene;

import javafx.scene.control.ComboBox;

import javafx.scene.control.TableColumn;

import javafx.scene.control.TableView;

import javafx.scene.control.cell.PropertyValueFactory;

import javafx.scene.layout.VBox;

import javafx.stage.Stage;

public class Main extends Application {

	@Override
    public void start(Stage primaryStage) {
        ComboBox<String> yearComboBox = createComboBox();

        TableView<DriverResult> tableView = new TableView<>();

        TableColumn<DriverResult, String> driverNameColumn = new TableColumn<>("Driver Name");
        driverNameColumn.setCellValueFactory(new PropertyValueFactory<>("driverName"));

        TableColumn<DriverResult, Integer> winsColumn = new TableColumn<>("Wins");
        winsColumn.setCellValueFactory(new PropertyValueFactory<>("wins"));

        TableColumn<DriverResult, Integer> totalPointsColumn = new TableColumn<>("Total Points");
        totalPointsColumn.setCellValueFactory(new PropertyValueFactory<>("totalPoints"));

        TableColumn<DriverResult, Integer> rankColumn = new TableColumn<>("Rank");
        rankColumn.setCellValueFactory(new PropertyValueFactory<>("rank"));

        tableView.getColumns().add(driverNameColumn);
        tableView.getColumns().add(winsColumn);
        tableView.getColumns().add(totalPointsColumn);
        tableView.getColumns().add(rankColumn);

        yearComboBox.valueProperty().addListener((observable, oldValue, newValue) -> {
            if (newValue != null) {
                int selectedYear = Integer.parseInt(newValue);
                DriverResultRepository driverResultRepository = new DriverResultRepository();
                var results = driverResultRepository.getResultByYear(selectedYear);
                tableView.setItems(FXCollections.observableArrayList(results));
            }
        });

        VBox vbox = new VBox(yearComboBox, tableView);
        Scene scene = new Scene(vbox, 400, 400);

        primaryStage.setScene(scene);
        primaryStage.setTitle("Driver Results");
        primaryStage.show();
    }

    public ComboBox<String> createComboBox() {
        ComboBox<String> comboBox = new ComboBox<>();
        comboBox.setPrefSize(100, 10);

        SeasonRepository seasonRepository = new SeasonRepository();
        List<Season> years = seasonRepository.getSeasons().stream()
                .sorted((s1, s2) -> Integer.compare(s1.getYear(), s2.getYear()))
                .collect(Collectors.toList());;
        

        for (Season season : years) {
            comboBox.getItems().add(String.valueOf(season.getYear()));
        }

        comboBox.valueProperty().addListener((observable, oldValue, newValue) -> {
            System.out.println("Ha seleccionado el año: " + newValue );	            
        });

        return comboBox;
    }

    public static void main(String[] args) {
        launch(args);
    }
}
