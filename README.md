# fxcontrol
My custom controls for javafx

## FXTableViewSupport Example
'''
	FXTableViewSupport<Booking> tableConfigure = FXTableViewSupport.forTableView(tableViewBooking).apply();
	tableConfigure.setSelectionMode(SelectionMode.MULTIPLE)
	.enableFilter(0, false, false)
	.enableFilter("FormatBookDate", true, false)
	.enableFilter("Id", true, false)
	.enableFilter("FormatCreatedDate", true, false)
	.enableFilter("FormatProcess", true, false)
	.enableFilter("FormatBookingType", true, false)
	.enableFilter("FormatAccount", true, false)
	.enableFilter("FormatStatus", true, false)
	.setAlignment("FormatAmount", "center-right")
	.setAlignment("Id", "center")
	.setAlignment("FormatBookingType", "center")
	.setAlignment("FormatTaxRate", "center")
	.setAlignment("FormatTaxValue", "center")
	.enableContextMenu(true)
	.addSelectionChanged(this::onTableViewBookingSelection)
	.addMenuCopyListener(items -> tableConfigure.getMasterData().addAll(items))
	.addMenuDeleteListener(this::onMenuItemDeleteBookingAction)
	.addMenuDuplicateListener(this::onMenuItemDuplicateBookingAction)
	.addMenuPasteListener(items -> tableConfigure.getMasterData().addAll(items))
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

	tableConfigure.findColumn(0).setCellFactory(e -> new BookingItemTableCell());
	tableConfigure.findColumn(12).setCellFactory(e -> new BookingActionCell());

	tableViewBooking.setEditable(true);
'''
