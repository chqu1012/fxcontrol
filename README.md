# fxcontrol
My custom controls for javafx

<details>
  <summary>FXTableViewSupport</summary>
Requirements: Each TableColumns should be defined by id with the attribute field, e.g. "FormatBookDate"

```java
FXTableViewSupport<Booking> tableConfigure = FXTableViewSupport.forTableView(tableViewBooking).apply();
tableConfigure.setSelectionMode(SelectionMode.MULTIPLE)
// Enable TextFilter and ChoiceFilter by column index or column id name
.enableFilter(0, false, false)
.enableFilter("FormatBookDate", true, false)
.enableFilter("Id", true, false)
.enableFilter("FormatCreatedDate", true, false)
.enableFilter("FormatProcess", true, false)
.enableFilter("FormatBookingType", true, false)
.enableFilter("FormatAccount", true, false)
.enableFilter("FormatStatus", true, false)

// Initialize Column alignment by column id name
.setAlignment("FormatAmount", "center-right")
.setAlignment("Id", "center")
.setAlignment("FormatBookingType", "center")
.setAlignment("FormatTaxRate", "center")
.setAlignment("FormatTaxValue", "center")

// Enable the table view context menu, otherwise popup is hidden
.enableContextMenu(true)

// Handle on selection change
.addSelectionChanged(this::onTableViewBookingSelection)

// Add action to copy, paste, duplicate and delete menu item
.addMenuCopyListener(items -> tableConfigure.getMasterData().addAll(items))
.addMenuDeleteListener(this::onMenuItemDeleteBookingAction)
.addMenuDuplicateListener(this::onMenuItemDuplicateBookingAction)
.addMenuPasteListener(items -> tableConfigure.getMasterData().addAll(items))

// Enable column editing with preferred dataype, here it is String, by column index
.enableStringColumnEdit(4, e->{ 
	e.getDataClass().setAmount(Double.parseDouble(e.getValue()));
	restService.save(e.getDataClass());
})
.enableStringColumnEdit(5, e->{ 
	e.getDataClass().setBookDate(LocalDate.parse(e.getValue(), DateTimeFormatter.ofPattern("dd.MM.yyyy")));
	restService.save(e.getDataClass());
})
.enableStringColumnEdit(7, e->{ 
	e.getDataClass().setDescription(e.getValue());
	restService.save(e.getDataClass());
});

// Set CellFactory by column index
tableConfigure.findColumn(0).setCellFactory(e -> new BookingItemTableCell());
tableConfigure.findColumn(12).setCellFactory(e -> new BookingActionCell());

tableViewBooking.setEditable(true);
```

<details>
  <summary>Data model for the TableView</summary>
  
 ```java
public class Booking extends BaseIdEntity {

	private BookingProcess process;
	private Client client;
	private Account account;
	private BookingType bookingType;
	private double amount;
	private double taxRate;
	private double taxValue;
	private String description;
	private LocalDate bookDate;

	public Booking() {
	}

	public Booking(Booking booking) {
		this.process = booking.getProcess();
		this.client = booking.getClient();
		this.account = booking.getAccount();
		this.bookingType = booking.getBookingType();
		this.amount = booking.getAmount();
		this.taxRate = booking.getTaxRate();
		this.description = booking.description;
		this.bookDate = booking.getBookDate();
		this.taxValue = booking.getTaxValue();
		this.setCreatedDate(LocalDateTime.now());
		this.setDeleted(false);
		this.setStatus(Status.NEW);
	}
	
	public double getTaxValue() {
		return taxValue;
	}

	public void setTaxValue(double taxValue) {
		this.taxValue = taxValue;
	}

	public Account getAccount() {
		return account;
	}

	public void setAccount(Account account) {
		this.account = account;
	}

	public BookingProcess getProcess() {
		return process;
	}

	public void setProcess(BookingProcess process) {
		this.process = process;
	}

	public Client getClient() {
		return client;
	}

	public void setClient(Client client) {
		this.client = client;
	}

	public BookingType getBookingType() {
		return bookingType;
	}

	public void setBookingType(BookingType bookingType) {
		this.bookingType = bookingType;
	}

	public double getAmount() {
		return amount;
	}

	public void setAmount(double amount) {
		this.amount = amount;
		if (amount > 0) {
			setBookingType(BookingType.INCOME);
		}else {
			setBookingType(BookingType.OUTPUT);
		}
	}

	public double getTaxRate() {
		return taxRate;
	}

	public void setTaxRate(double taxRate) {
		this.taxRate = taxRate;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}
	
	public LocalDate getBookDate() {
		return bookDate;
	}

	public void setBookDate(LocalDate bookDate) {
		this.bookDate = bookDate;
	}

	public String getFormatBookDate() {
		return bookDate.format(DateTimeFormatter.ofPattern("dd.MM.yyyy"));
	}

	public String getFormatCreatedDate() {
		return getCreatedDate().format(DateTimeFormatter.ofPattern("dd.MM.yyyy"));
	}
	
	public String getFormatTaxRate() {
		return String.format("%.2f €", taxRate);
	}

	public String getFormatAmount() {
		return String.format("%.2f €", amount);
	}

	public String getFormatProcess() {
		return process.getName();
	}

	public String getFormatAccount() {
		return account.getName();
	}

	public String getFormatTaxValue() {
		return String.format("%.2f €", taxValue);
	}

	public String getFormatBookingType() {
		return bookingType==BookingType.INCOME ? "Einnahme" : "Ausgabe";
	}
	
	public String getFormatStatus() {
		Status status = getStatus();
		if (status==null) {
			return Status.ACTIV.name();
		}
		return status.name();
	}
}
```
</details>
</details>

<details>
  <summary>FXH2Console</summary>
This pane opens the h2 admin console in a pane. The user can queries the result in this console.

* requires h2 lib: http://www.h2database.com/html/download.html
* Start the h2 server

How to create a H2 console

```java
FXH2Console console = FXH2Console.create().apply()
	.setHost("localhost")
	.setPort("8889")
	.setPath("h2-console");
```
</details>

<details>
  <summary>FXProposals for TextFields</summary>
To use own objects, they must extends from IFXProposal. The value will be the value of the text and the description will shown the hint for the proposal in the user interface.
	
```java
public class FXProposalApplication extends FXApplication{

	public class Person implements IFXProposal<Person>{
		String name;

		public Person(String name) {
			this.name = name;
		}
		
		@Override
		public String getValue() {
			return name;
		}

		@Override
		public String getDescription() {
			return "Small Proposal";
		}

		@Override
		public int compareTo(Person o) {
			return 0;
		}
	}
	
	@Override
	protected Parent getRoot() {
		TextField textField = new TextField();
		ObservableList<Person> listPerson = FXCollections.observableArrayList();
		listPerson.add(new Person("Peter Jackson"));
		listPerson.add(new Person("Christopher Nolan"));
		listPerson.add(new Person("Lee Yun Yee"));
		FXProposalSupport.forTextField(textField).apply().setPropoals(listPerson);
		return textField;
	}
}
```
</details>
