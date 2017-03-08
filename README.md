PHP_SAMPLES

These are code fragments from some PHP projects I've worked on in the past...



This code queryed the database and returns a list of available work near the logged in user in order for them to view and bid on jobs.

    $result = caspioProximitySearchByCoordinatesRaw("Work_Orders_Geocode", false, $Zip[0], $Zip[1], "Latitude", "Longitude", "<= $Radius.49", "3", "TB_UNID, StartDate, StartTime, City, State, Zipcode, PayMax, Amount_Per, Description, Requirements, Tools, SpecialInstructions", "AND IsNull(Tech_ID, '') = '' AND IsNull(Deactivated, '') = '' AND ShowTechs = '1'", "", false, "|", "`", false);
    
    if (sizeof($result) == 1 && $result[0] == "") $result = array();
    
    foreach ($result as $row) {
    $workOrder = explode("|", $row);
    	
    	echo '<item><title>New Work Order - ' . htmlentities(trim($workOrder[3], "`")) . '</title>';
    	echo '<link>https://www.xxxxxx.com/techs/wosDetails.php?id=' . trim($workOrder[0], "`") . '</link>';
    	echo '<guid>https://www.xxxxxx.com/techs/wosDetails.php?id=' . trim($workOrder[0], "`") . '</guid>';
    	echo '<pubDate>' .  date("r") . '</pubDate>';
    	echo '<description>';
    	echo '<b>Work Description:</b><br /> ' . htmlentities(trim($workOrder[8], "`")) . '<br /><br />';
    	echo '<b>Work Requirements:</b><br /> ' . htmlentities(trim($workOrder[9], "`")) . '<br /><br />';
    	echo '<b>Work Tools:</b><br /> ' . html_entity_decode(trim($workOrder[10], "`")) . '<br /><br />';
    	echo '<b>Work Special Instructions:</b><br /> ' . html_entity_decode(trim($workOrder[11], "`"));
    	
    	echo '</description>';
    	echo '</item>';
    		
    }

    

JavaScript mapping code for a php application:
This creates a google map from a JSON object of locations and color codes the location based upon company location type.

    // header info...
    .
    .
    .
    
    <h1>Our Locations</h1>
    <div class="container container-fluid" style="height: 75%; width: 75%;">
    	<div id="map" style="border-style: solid; border-width: 1px;"></div>
    </div>
    </script><script src="https://maps.googleapis.com/maps/api/js?key={INSERT API KEY HERE}"></script>

    <script>
    	var labels = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    	var labelIndex = 0;
    
    	function initialize() {
    		var map = new google.maps.Map(document.getElementById('map'));
    		map.setZoom(4);
    		map.setCenter(new google.maps.LatLng(36.500, -95.5795));
    
    		var opt = {
    			minZoom: 4,
    			maxZoom: 15
    		};
    		map.setOptions(opt);
    
    		var geocoder = new google.maps.Geocoder();
    
    		function getLocFlag(locName) {
    			switch (locName) {
    				case "xxxx xxxxxx":
    					return 'http://maps.google.com/mapfiles/ms/icons/blue-dot.png';
    					break;
    				case "Provider Partner":
    					return 'http://maps.google.com/mapfiles/ms/icons/green-dot.png';
    					break;
    				case "Coming Soon":
    					return 'http://maps.google.com/mapfiles/ms/icons/orange-dot.png';
    					break;
    			}
    		}
    
    		for (var i = 0; i < locations.length; i++) {
    			// for (var i = 6; i < 12; i++) {
    			var address = locations[i].address + ", " + locations[i].city + ", " + locations[i].state + ", " + locations[i].zip;
    			var address = address.replace(/(^,)|(, $)/g, "");
    			url = getLocFlag(locations[i].type);
    
    			var image = {
    				url: url,
    				// This marker is 32 pixels wide by 32 pixels high.
    				size: new google.maps.Size(32, 32),
    				// The origin for this image is (0, 0).
    				origin: new google.maps.Point(0, 0),
    				// The anchor for this image is the base of the flagpole at (0, 32).
    				anchor: new google.maps.Point(0, 32)
    			};
    
    			var shape = {
    				coords: [1, 1, 1, 20, 18, 20, 18, 1],
    				type: 'poly'
    			};
    
    			function attachLocInfo(marker, locAddress) {
    				var formattedAddress = locAddress.replace(/(^,)|(, $)/g, "<br>");
    				// console.log(formattedAddress);
    
    				var infowindow = new google.maps.InfoWindow({
    					content: formattedAddress
    				});
    
    				marker.addListener('mouseover', function() {
    					infowindow.open(marker.get('map'), marker);
    				});
    
    				marker.addListener('mouseout', function() {
    					infowindow.close();
    				});
    			}
    
    			if (locations[i].lat == "") {
    				geocoder.geocode({
    					'address': address
    				}, function(results, status) {
    					if (status === 'OK') {
    						console.log("New Geocode: " + results[0].formatted_address + ": " + results[0].geometry.location);
    						var marker = new google.maps.Marker({
    							map: map,
    							position: results[0].geometry.location,
    							icon: image,
    							shape: shape
    						});
    						attachLocInfo(marker, results[0].formatted_address);
    					} else {
    						console.log('Geocode was not successful for the following reason: ' + status);
    					}
    				})
    			} else {
    				// console.log("Lat: " + locations[i].lat);
    				var loc = {
    					lat: locations[i].lat,
    					lng: locations[i].lng
    				};
    				// console.log(loc);
    
    				var marker = new google.maps.Marker({
    					map: map,
    					position: loc,
    					icon: image,
    					shape: shape
    				});

    				var locAddress = "<address><span class='infoWindow'>" + locations[i].type + "</span><br>";
    				if (locations[i].address != "") {
    					locAddress += locations[i].address + "<br>"
    				}
    				if (locations[i].address2 != "") {
    					locAddress += locations[i].address2 + "<br>"
    				}
    				locAddress += locations[i].city + ", " + locations[i].state + " " + locations[i].zip + "<address>";
    
    				attachLocInfo(marker, locAddress)
    			}
    		}
    	}
    	
    	initialize();
    .
    .
    .
    
    	<!-- Bootstrap core JavaScript
    	================================================== -->
    	<!-- Placed at the end of the document so the pages load faster -->
    	<script src="assets/js/jquery-1.10.2.min.js"></script>
    	<script src="assets/js/bootstrap.js"></script>
    	<script src="assets/js/waypoints.min.js"></script>
    	<script src="assets/js/jquery.scrollto.min.js"></script>
    	<script src="assets/js/jquery.localscroll.min.js"></script>
    	<script src="assets/js/jquery.prettyPhoto.js"></script>
    	<script src="assets/js/scripts.js"></script>
    
