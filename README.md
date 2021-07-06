# pick8chu.github.io


```

//Create a Region
var pinRegion = Region(?, ?, ?, ?)
var confirmRegion = Region(?, ?, ?, ?)
i = 1

do {

	// click first
	click(Point(?, ?), CParam.hold(0).repeat(1).delay(0).waitNext(100).random(0))

	//Click on the template
	var match = pinRegion.click("pin", FParam.timeout(0).sRate(3).mScore(0.7))

	if (match) {
		//Success
		
		do {
			// click first
			click(Point(?, ?), CParam.hold(0).repeat(1).delay(0).waitNext(100).random(0))

			//Click on the template
			confirmRegion.click("confirm", FParam.timeout(1000).sRate(3).mScore(0.7))
		} while (i)
		
	} else {
		//Fail
	}

} while (i)
	

```
