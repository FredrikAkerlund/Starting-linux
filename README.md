# Saitti
## Hei maailma harjoitus tehtävä

 >Testitekstia Lorem Ipsum!!

### Joskus kannatta katsoa googlesta vastauksia!!
`cd, ls, pwd, git `

## Lisäksi alla teille ohjelma!


`<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Valmistumisjuhlat</title>
<style type="text/css">
label {
	display: block;
	width: 11em;
	float: left;
}
</style>
<!-- 
Ohjelma kysyy valmistumisjuhliin kutsuttujen määrän ja laskee juhlissa 
tarvittavien kuohuviinipullojen määrän. Yhdestä pullosta saa 7 lasillista. 
Ohjelma kysyy lisäksi yksittäisen kuohuviinipullon hinnan sekä pitopalvelun hinnan per kutsuttu
ja laskee juhlien kustannukset.
 -->
</head>
<body>
	<h3>Valmistumisjuhlat</h3>

	<form>
		<p>
			<label>Kutsuttujen määrä:</label> 
			<input type="text" id="maara" size="3"> 
		</p>
		<p>
			<label>Yksittäisen pullon hinta:</label> 
			<input type="text" id="pullonhinta" size="5">
		</p>
		<p>
			<label>Pitopalvelu per henkilö:</label> 
			<input type="text" id="pitopalvelunhinta" size="5">
		</p>
		<p>
			<label>Tuonti paikanpäälle:</label> 
			<input type="checkbox" id="kotiin">
		</p>

		<input type="button" value="Laske" onclick="laskeKustannus()">
	</form>

	<!-- Tänne kirjoitetaan vastaus JavaScriptillä -->
	<div id="vastaus"></div>

	<script type="text/javascript">
		// Funktio
		function laskeKustannus() { 
			// Lomakekentän arvo
			var maara = document.getElementById("maara").value;
			var pullot = Math.ceil(maara / 7);

			var pullonhinta = document.getElementById("pullonhinta").value;
			var juomat = pullot * pullonhinta;

			var pitopalvelunhinta = document.getElementById("pitopalvelunhinta").value;
			var pitopalvelu = maara * pitopalvelunhinta;

			var kotiin = 0;

			// jos checkbox (valintaruutu) on valittuna
			if (document.getElementById("kotiin").checked == true) {
				kotiin = 20;
			}

			var yhteensa = juomat + pitopalvelu + kotiin;
			// Vastauksen kirjoittaminen html-sivulle
			document.getElementById("vastaus").innerHTML = "<p>Tarvitaan " + 
					pullot + " pulloa.<br>Juomat " + juomat.toFixed(2) + 
					" euroa.<br/>" + "Pitopalvelu " + pitopalvelu.toFixed(2) + 
					" euroa.<br>Kotiinkuljetus " + kotiin + " euroa.<br>" + 
					"YHTEENSÄ " + yhteensa.toFixed(2) + " euroa</p>";
		}
	</script>
</body>
</html>`
