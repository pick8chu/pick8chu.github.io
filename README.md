# pick8chu.github.io


```

//Create a Region
var pinRegion = Region(14, 483, 1064, 1213)
var confirmRegion = Region(170, 659, 857, 849)
i = 1

do {

	// click first
	click(Point(807, 1941), CParam.hold(0).repeat(1).delay(0).waitNext(100).random(0))

	//Click on the template
	var match = pinRegion.click("pin", FParam.timeout(0).sRate(3).mScore(0.8))

	if (match) {
		//Success
		
		do {
			// click first
			click(Point(807, 1941), CParam.hold(0).repeat(1).delay(0).waitNext(100).random(0))

			//Click on the template
			confirmRegion.click("confirm", FParam.timeout(1000).sRate(3).mScore(0.7))
		} while (i)
		
	} else {
		//Fail
	}

} while (i)
	

```
