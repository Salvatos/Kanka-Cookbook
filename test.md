<script>
	window.onload = function() {
		// Get URL query string parameters and apply to form and output
		let params = (new URL(location)).searchParams;
		for (let p of params) {
			switch(p[0]) {
				case "elite":
					var elitecol = (p[1] == "true" || p[1] == 1) ? "#5f3198" : "#6e1c1e";
					document.getElementById("titlebar").style.backgroundColor = elitecol;
					document.getElementById("arrow-up").style.borderBottom = '7px solid ' + elitecol;
					document.getElementById("elite-in").checked = (p[1] == "true") ? true : false;
					break;
				case "attacks":
				case "abilities":
					document.getElementById(p[0]+"-in").value = p[1];
					pushText(document.getElementById(p[0]+"-in"), p[0]+"-out");
					break;
				default:
					document.getElementById(p[0]+"-in").value = p[1];
					pushValue(document.getElementById(p[0]+"-in"), p[0]+"-out");
					break;
			}
		}
		// Switch stat preset to appropriate danger level
		pushPreset(document.getElementById("dl-in"));
	}

	function pushValue(source, target) {
		document.getElementById(target).innerHTML = source.value;
	}
	function pushText(source, target) {
		text = source.value.replace(/\n+/g, "<br />");
		document.getElementById(target).innerHTML = text;
	}
	function pushElite(source) {
		var bgcol = source.checked ? "#5f3198" : "#6e1c1e";
		document.getElementById("titlebar").style.backgroundColor = bgcol;
		document.getElementById("arrow-up").style.borderBottom = '7px solid ' + bgcol;
	}
	function pushPreset(source) {
		switch(parseInt(source.value)) {
			case 1:
				document.getElementById("presets").innerHTML = "<th>1</th><td>10&ndash;19</td><td>0&ndash;1</td><td>11&ndash;13</td><td>11&ndash;13</td><td>13&ndash;16</td><td>11&ndash;14</td><td>+1&ndash;3</td><td>10&ndash;20</td>";
				break;
			case 2:
				document.getElementById("presets").innerHTML = "<th>2</th><td>14&ndash;22</td><td>0&ndash;2</td><td>11&ndash;13</td><td>11&ndash;13</td><td>14&ndash;16</td><td>12&ndash;14</td><td>+2&ndash;3</td><td>20&ndash;40</td>";
				break;
			case 3:
				document.getElementById("presets").innerHTML = "<th>3</th><td>16&ndash;25</td><td>0&ndash;2</td><td>12&ndash;15</td><td>12&ndash;15</td><td>15&ndash;17</td><td>13&ndash;15</td><td>+2&ndash;4</td><td>40&ndash;60</td>";
				break;
			case 4:
				document.getElementById("presets").innerHTML = "<th>4</th><td>20&ndash;30</td><td>0&ndash;3</td><td>12&ndash;16</td><td>12&ndash;16</td><td>16&ndash;18</td><td>14&ndash;16</td><td>+3&ndash;4</td><td>60&ndash;100</td>";
				break;
			case 5:
				document.getElementById("presets").innerHTML = "<th>5</th><td>28&ndash;40</td><td>0&ndash;3</td><td>13&ndash;16</td><td>13&ndash;16</td><td>17&ndash;20</td><td>15&ndash;18</td><td>+4&ndash;5</td><td>100&ndash;150</td>";
				break;
			case 6:
				document.getElementById("presets").innerHTML = "<th>6</th><td>40&ndash;60</td><td>0&ndash;4</td><td>13&ndash;16</td><td>13&ndash;16</td><td>18&ndash;21</td><td>16&ndash;19</td><td>+5&ndash;7</td><td>150&ndash;200</td>";
				break;
			case 7:
				document.getElementById("presets").innerHTML = "<th>7</th><td>50&ndash;70</td><td>0&ndash;4</td><td>14&ndash;17</td><td>14&ndash;17</td><td>19&ndash;22</td><td>17&ndash;20</td><td>+6&ndash;7</td><td>200&ndash;250</td>";
				break;
			case 8:
				document.getElementById("presets").innerHTML = "<th>8</th><td>57&ndash;78</td><td>0&ndash;5</td><td>14&ndash;18</td><td>14&ndash;18</td><td>19&ndash;22</td><td>17&ndash;20</td><td>+8&ndash;9</td><td>250&ndash;300</td>";
				break;
			case 9:
				document.getElementById("presets").innerHTML = "<th>9</th><td>63&ndash;83</td><td>0&ndash;5</td><td>15&ndash;18</td><td>15&ndash;18</td><td>21&ndash;23</td><td>19&ndash;21</td><td>+8&ndash;10</td><td>300&ndash;350</td>";
				break;
			case 10:
				document.getElementById("presets").innerHTML = "<th>10</th><td>65&ndash;85</td><td>0&ndash;6</td><td>15&ndash;20</td><td>15&ndash;20</td><td>22&ndash;25</td><td>20&ndash;23</td><td>+9&ndash;10</td><td>350&ndash;400</td>";
				break;
			default:
				document.getElementById("presets").innerHTML = "<td colspan=10>Error</td>";
		}
	}
	function calcStat(source, target, stat, input) { // Run common formula for stat scaling
		output = (((input - source[stat]) / source[stat]) * target[stat]) + target[stat];
		return Math.round(output);
	}
	function insertTags(field) {
		if (field == "attack") {
			blockId = "attacks-in";
			insertString = "<b>Name:</b> <i>Range. Damage.</i>";
		}
		else {
			blockId = "abilities-in";
			insertString = "<b>Name:</b> <i>Action. Range. Ruin. r.</i> Description.";
		}

		// Add line break as necessary
		testbreak = document.getElementById(blockId).value;
		if (testbreak.endsWith('\n') || testbreak.endsWith('\r') || testbreak.endsWith('\r\n') || testbreak == "") {
			newline = "";
		}
		else {
		    newline = "\r\n";
		}

		document.getElementById(blockId).value += newline + insertString;
	}
	function convertStats() { // Run all stat conversions at once
		presets = [
		{dl:0, hp: 0, av: 0, spd: 0, ar: 0, dr: 0, mr: 0, dmg: 0, xp:0}, // dummy array for indexing purposes (so DL 1 = [1])
		{dl:1, hp: 14.5, av: 0.5, spd: 12, ar: 12, dr: 14.5, mr: 12.5, dmg: 2, xp:15},
		{dl:2, hp: 18, av: 1, spd: 12, ar: 12, dr: 15, mr: 13, dmg: 2.5, xp:30},
		{dl:3, hp: 20.5, av: 1, spd: 13.5, ar: 13.5, dr: 16, mr: 14, dmg: 3, xp:50},
		{dl:4, hp: 25, av: 1.5, spd: 14, ar: 14, dr: 17, mr: 15, dmg: 3.5, xp:80},
		{dl:5, hp: 34, av: 1.5, spd: 14.5, ar: 14.5, dr: 18.5, mr: 16.5, dmg: 4.5, xp:125},
		{dl:6, hp: 50, av: 2, spd: 14.5, ar: 14.5, dr: 19.5, mr: 17.5, dmg: 6, xp:175},
		{dl:7, hp: 60, av: 2, spd: 15.5, ar: 15.5, dr: 20.5, mr: 18.5, dmg: 6.5, xp:225},
		{dl:8, hp: 67.5, av: 2.5, spd: 16, ar: 16, dr: 20.5, mr: 18.5, dmg: 8.5, xp:275},
		{dl:9, hp: 73, av: 2.5, spd: 16.5, ar: 16.5, dr: 22, mr: 20, dmg: 9, xp:325},
		{dl:10, hp: 75, av: 3, spd: 17.5, ar: 17.5, dr: 23.5, mr: 21.5, dmg: 9.5, xp:375}
		];
		dmgSpread = ['1&ndash;3', '2&ndash;3', '2&ndash;4', '3&ndash;4', '4&ndash;5', '5&ndash;7', '6&ndash;7', '8&ndash;9', '8&ndash;10', '9&ndash;10'];

		sourcedl = document.getElementById('dl-source').value;
		targetdl = document.getElementById('dl-target').value;
		hp = calcStat(presets[sourcedl], presets[targetdl], 'hp', document.getElementById('hp-in').value);
		av = calcStat(presets[sourcedl], presets[targetdl], 'av', document.getElementById('av-in').value);
		spd = calcStat(presets[sourcedl], presets[targetdl], 'spd', document.getElementById('spd-in').value);
		ar = calcStat(presets[sourcedl], presets[targetdl], 'ar', document.getElementById('ar-in').value);
		dr = calcStat(presets[sourcedl], presets[targetdl], 'dr', document.getElementById('dr-in').value);
		mr = calcStat(presets[sourcedl], presets[targetdl], 'mr', document.getElementById('mr-in').value);
		//dmg = calcStat(presets[sourcedl], presets[targetdl], 'dmg', document.getElementById('dmg-in').value); // for reference; currently showing suggested range regardless of initial values
		dmg = dmgSpread[targetdl];
		xp = calcStat(presets[sourcedl], presets[targetdl], 'xp', document.getElementById('xp-in').value);

		document.getElementById("scale-outputs").innerHTML = "<th>"+targetdl+"</th><td>"+hp+"</td><td>"+av+"</td><td>"+spd+"</td><td>"+ar+"</td><td>"+dr+"</td><td>"+mr+"</td><td>+"+dmg+"</td><td>"+xp+"</td>";
		document.getElementById("scale-output").style.display = "table";
	}
	function makeURL() { // for export
		var querystring =
			//window.location.hostname+window.location.pathname+
			'brewnity.ca/npc'+
			"?name="    +document.getElementById("name-in").value+
			"&types="   +document.getElementById("types-in").value+
			"&dl="      +document.getElementById("dl-in").value+
			"&elite="   +document.getElementById("elite-in").checked+
			"&xp="      +document.getElementById("xp-in").value+
			"&might="   +document.getElementById("might-in").value+
			"&agi="     +document.getElementById("agi-in").value+
			"&mind="    +document.getElementById("mind-in").value+
			"&pres="    +document.getElementById("pres-in").value+
			"&hp="      +document.getElementById("hp-in").value+
			"&av="      +document.getElementById("av-in").value+
			"&spd="     +document.getElementById("spd-in").value+
			"&ar="      +document.getElementById("ar-in").value+
			"&dr="      +document.getElementById("dr-in").value+
			"&mr="      +document.getElementById("mr-in").value+
			"&attacks=" +document.getElementById("attacks-in").value.replace(/\n/g, "%0A").replace(/\+/g, "%2B").replace(/\#/g, "%23")+
			"&abilities="+document.getElementById("abilities-in").value.replace(/\n/g, "%0A").replace(/\+/g, "%2B").replace(/\#/g, "%23");

		document.getElementById("URL-out").innerHTML = querystring;
		document.getElementById("URL-out").href = "http://" + querystring;
	}
	function makeIMG() { // for export
		html2canvas(document.getElementById("export"), {backgroundColor: null, scale: 1}).then(function(canvas) {
		    if (document.getElementById("canvas").innerHTML != "") { // Replace existing export if any
				document.getElementById("canvas").replaceChild(canvas, document.getElementById("canvas").children[0]);
			}
		    else {
			    document.getElementById("canvas").appendChild(canvas);
		    }
		});
	}
	</script>
	<div id="intro">
		<h1>UNITY NPC Creator</h1>
		<p>This tool allows you to prepare tailor-made monsters and NPCs for Unity. All values you enter will transfer to a template similar to those found in the core book, which you can then save as an image or share using a custom URL. Appearance may vary based on your browser, and JavaScript must be enabled. For bug reports, suggestions and comments, or to give your creature a permanent page on the site, <a href="/contact">send me an e-mail</a>.</p>
		<details>
			<summary><h3 class="ridged">Instructions</h3></summary>
			<p>Fill in the form on the left and the template will be populated automatically. Selecting a Danger Level will show suggested stat presets for non-Elite<sup class="callnote">&nbsp;<a href="#note1">[1]</a></sup> creatures of that DL, based on the table on p.&nbsp;309 of the core book. You can use the arrow keys to increment the values of numeric fields.</p>
			<p>In addition, you can enter the stats of any existing NPC and use the <i>"Scale combat stats..."</i> feature to instantly eyeball equivalent values for a different Danger Level, then make those changes (adjusting as necessary) in the form. Keep in mind that attribute scores, damage dice and abilities in general also factor into an NPC's power level, so scaling combat stats should only be one part of your scaling process.</p>
		</details>
	</div>

	<div class="flex-dual">
	<div class="column">
		<form>
			<div class="fieldrow">
				<div>
					<h4>Name</h4>
					<input id="name-in" type="text" size="20" onkeyup="pushValue(this, 'name-out')" onchange="pushValue(this, 'name-out')">
				</div>
				<div>
					<h4>DL</h4>
					<select id="dl-in" onchange="pushValue(this, 'dl-out'), pushPreset(this)">
					<option value="1">1</option>
					<option value="2">2</option>
					<option value="3">3</option>
					<option value="4">4</option>
					<option value="5">5</option>
					<option value="6">6</option>
					<option value="7">7</option>
					<option value="8">8</option>
					<option value="9">9</option>
					<option value="10">10</option>
					</select>
				</div>
			</div>

			<div class="fieldrow">
				<div>
					<h4><acronym title="Tiny, Small, Medium, Large, Massive, Colossal">Size</acronym> and <acronym title="Beast, Demon, Humanoid, Mechanical, Undead">Type</acronym></h4>
					<input id="types-in" type="text" size="20" onkeyup="pushValue(this, 'types-out')" onchange="pushValue(this, 'types-out')">
				</div>
				<div>
					<h4>Elite?</h4>
					<input id="elite-in" name="elite" onchange="pushElite(this)" type="checkbox">
				</div>
			</div>

			<div class="fieldrow fieldrow-center fieldrow-5">
				<div>
					<h4>MIGHT</h4>
					<input id="might-in" min="0" max="99" step="1" onkeyup="pushValue(this, 'might-out')" onchange="pushValue(this, 'might-out')" type="number">
				</div>
				<div>
					<h4>AGI</h4>
					<input id="agi-in" min="0" max="99" step="1" onkeyup="pushValue(this, 'agi-out')" onchange="pushValue(this, 'agi-out')" type="number">
				</div>
				<div>
					<h4>MIND</h4>
					<input id="mind-in" min="0" max="99" step="1" onkeyup="pushValue(this, 'mind-out')" onchange="pushValue(this, 'mind-out')" type="number">
				</div>
				<div>
					<h4>PRES</h4>
					<input id="pres-in" min="0" max="99" step="1" onkeyup="pushValue(this, 'pres-out')" onchange="pushValue(this, 'pres-out')" type="number">
				</div>
				<div>
					<h4>XP</h4>
					<input id="xp-in" min="0" max="9999" step="5" onkeyup="pushValue(this, 'xp-out')" onchange="pushValue(this, 'xp-out')" type="number">
				</div>
			</div>

			<p class="maintext" style="margin: 0; max-width: 30rem; font-style: italic;">(An NPC’s highest attribute is typically equal to its DL, with an approximate spread of e.g. 3-2-2-1 or 6-4-2-0.)</p>

			<div class="fieldrow fieldrow-center fieldrow-4">
				<div>
					<h4>HP</h4>
					<input id="hp-in" type="number" min="0" max="999" step="1" onkeyup="pushValue(this, 'hp')" onchange="pushValue(this, 'hp-out')">
				</div>
				<div>
					<h4>AV</h4>
					<input id="av-in" type="number" min="0" max="99" step="1" onkeyup="pushValue(this, 'av')" onchange="pushValue(this, 'av-out')">
				</div>
				<div>
					<h4>SPD</h4>
					<input id="spd-in" type="number" min="0" max="99" step="1" onkeyup="pushValue(this, 'spd')" onchange="pushValue(this, 'spd-out')">
				</div>
				<div>
					<h4>AR</h4>
					<input id="ar-in" type="number" min="0" max="99" step="1" onkeyup="pushValue(this, 'ar')" onchange="pushValue(this, 'ar-out')">
				</div>
				<div>
					<h4>DR</h4>
					<input id="dr-in" type="number" min="0" max="99" step="1" onkeyup="pushValue(this, 'dr')" onchange="pushValue(this, 'dr-out')">
				</div>
				<div>
					<h4>MR</h4>
					<input id="mr-in" type="number" min="0" max="99" step="1" onkeyup="pushValue(this, 'mr')" onchange="pushValue(this, 'mr-out')">
				</div>
			</div>

			<h4>Stat presets (standard NPC<sup class="callnote">&nbsp;<a href="#note1">[1]</a></sup>)</h4>
			<table id="preset">
				<tr>
					<th>DL</th>
					<th>HP</th>
					<th>AV</th>
					<th>SPD</th>
					<th>AR</th>
					<th>DR</th>
					<th>MR</th>
					<th>DMG</th>
					<th>XP</th>
				</tr>
				<tr id="presets">
					<th>1</th>
					<td>10&ndash;19</td>
					<td>0&ndash;1</td>
					<td>11&ndash;13</td>
					<td>11&ndash;13</td>
					<td>13&ndash;16</td>
					<td>11&ndash;14</td>
					<td>+1&ndash;3</td>
					<td>10&ndash;20</td>
				</tr>
			</table>

			<h4>Scale combat stats from DL
				<select id="dl-source" onchange="convertStats()">
					<option value="1">1</option>
					<option value="2">2</option>
					<option value="3">3</option>
					<option value="4">4</option>
					<option value="5">5</option>
					<option value="6">6</option>
					<option value="7">7</option>
					<option value="8">8</option>
					<option value="9">9</option>
					<option value="10">10</option>
				</select>
				 to
				<select id="dl-target" onchange="convertStats()">
					<option value="1">1</option>
					<option value="2">2</option>
					<option value="3">3</option>
					<option value="4">4</option>
					<option value="5">5</option>
					<option value="6">6</option>
					<option value="7">7</option>
					<option value="8">8</option>
					<option value="9">9</option>
					<option value="10">10</option>
					</select>
			</h4>
			<table id="scale-output" style="display: none;">
				<tr>
					<th>DL</th>
					<th>HP</th>
					<th>AV</th>
					<th>SPD</th>
					<th>AR</th>
					<th>DR</th>
					<th>MR</th>
					<th>DMG</th>
					<th>XP</th>
				</tr>
				<tr id="scale-outputs">
					<th>1</th>
					<td>10&ndash;19</td>
					<td>0&ndash;1</td>
					<td>11&ndash;13</td>
					<td>11&ndash;13</td>
					<td>13&ndash;16</td>
					<td>11&ndash;14</td>
					<td>+1&ndash;3</td>
					<td>10&ndash;20</td>
				</tr>
			</table>

			<p class="maintext" style="margin: 1rem 0; max-width: 30rem;">Enter your attacks and abilities in the fields below. Click the cross to automatically insert tags for a new entry. Additional HTML tags can also be used for advanced formatting.</p>
			<h4>Attacks <a class="insert-tags" title="Insert tags" onclick="insertTags('attack')">&#x271a;</a></h4>
			<textarea id="attacks-in" rows="3" cols="65" onkeyup="pushText(this, 'attacks-out')" onchange="pushText(this, 'attacks-out')"></textarea>
			<h4>Tactics and Abilities <a class="insert-tags" title="Insert tags" onclick="insertTags('ability')">&#x271a;</a></h4>
			<textarea id="abilities-in" rows="15" cols="65" onkeyup="pushText(this, 'abilities-out')" onchange="pushText(this, 'abilities-out')"></textarea>
		</form>
	</div>

	<div class="column">
		<div id="export" style="padding: 1px 0 0 15px; width: 345px">
		<div id="npcblock">
			<div id="arrow-up"></div>
			<div id="titlebar">
				<p class="npc-name" id="name-out">Name</p>
				<p class="npc-types"><span id="types-out">Size, Type</span>, <span id="xp-out">#</span> XP</p>
				<div class="level" id="dl-out">#</div>
			</div>
			<div class="subtitle">
				COMBAT STATS
			</div>
			<div class="tablerow subsubheader stats">
				<div>
					<div class="subsubtitle">HP</div>
					<div class="subsubtitle">AV</div>
					<div class="subsubtitle">SPD</div>
					<div class="subsubtitle">AR</div>
					<div class="subsubtitle">DR</div>
					<div class="subsubtitle">MR</div>
				</div>
			</div>
			<div class="tablerow subsubrow stats">
				<div>
					<div class="numbers" id="hp-out">#</div>
					<div class="numbers" id="av-out">#</div>
					<div class="numbers" id="spd-out">#</div>
					<div class="numbers" id="ar-out">#</div>
					<div class="numbers" id="dr-out">#</div>
					<div class="numbers" id="mr-out">#</div>
				</div>
			</div>
			<div class="subtitle">
				ATTRIBUTES
			</div>
			<div class="tablerow subsubheader attributes">
				<div>
					<div class="subsubtitle">MIGHT</div>
					<div class="subsubtitle">AGILITY</div>
					<div class="subsubtitle">MIND</div>
					<div class="subsubtitle">PRESENCE</div>
				</div>
			</div>
			<div class="tablerow subsubrow attributes">
				<div>
					<div class="numbers" id="might-out">#</div>
					<div class="numbers" id="agi-out">#</div>
					<div class="numbers" id="mind-out">#</div>
					<div class="numbers" id="pres-out">#</div>
				</div>
			</div>
			<div class="subtitle">
				ATTACKS
			</div>
			<div class="blocktext">
				<p id="attacks-out"><b>Attack Name:</b> <i>Range. #d# damage.</i></p>
			</div>
			<div class="subtitle">
				TACTICS AND ABILITIES
			</div>
			<div class="blocktext">
				<p id="abilities-out"><b>Ability Name:</b> <i>Action cost. Range. # Ruin. #r.</i> Description.</p>
			</div>
		</div>
		</div>

		<div style="padding-left: 15px;">
			<p class="maintext">Use the buttons below to get a persistent link<sup class="callnote">&nbsp;<a href="#note2">[2]</a></sup> to this NPC or create an image copy<sup class="callnote">&nbsp;<a href="#note3">[3]</a></sup> of the NPC to save to your desktop.</p>
			<p><button onClick="makeURL()">Generate URL</button> <button onClick="makeIMG()">Generate image</button></p>
			<div id="url"><a href="" id="URL-out"></a></div>
		</div>

		<div id="canvas"></div>
	</div>
	</div>

	<div id="footnotes">
		<p class="footnote" id="note1"><sup class="callnote">1</sup> From personal observation, Elites seem to have about 5 times normal HP and 10 times normal XP. Their combat stats can be deceptively low (sometimes matching NPCs several DLs under theirs) to make up for their abilities and high HP, but some go just above the standard max for their DL. Their attributes tend to be quite high, the highest sometimes equal to twice their DL. Note that DL scaling should also work for Elites since it is not bound by the suggested limits.</p>
		<p class="footnote" id="note2"><sup class="callnote">2</sup> Right-click to copy the full URL. Note that URLs exceeding 2,000 characters may not work in some older browsers.</p>
		<p class="footnote" id="note3"><sup class="callnote">3</sup> Right-click > Save image as... <b>No image?</b> Image generation will fail if your HTML tags are not properly closed. Otherwise, check your <a href="https://caniuse.com/#search=canvas" target="_blank" class="external-link no-image">browser's compatibility</a> with the canvas element.</p>
	</div>

	<div id="release-notes">
		<details>
			<summary>Unity NPC Creator v.1.2 <span style="color: #286ba5;">Changelog</span></summary>
			<ul class="release-notes">
				<li><b>v.1.2</b> (2019-09-04)<br />
					<ul>
						<li>Added a tip from the rulebook regarding attributes relative to Danger Level.</li>
					</ul>
				</li>
				<li><b>v.1.1</b> (2018-06-20)<br />
					<ul>
						<li>Changed image export mechanism to properly include fonts; IE support uncertain, please let me know if you have issues with it.</li>
						<li>Added tag insertion buttons to save typing some HTML over and over.</li>
					</ul>
				</li>
				<li><b>v.1.0</b> (2018-06-13)<br />
					<ul>
						<li>Official release on Brewnity. Slightly different appearance, but same functionality and old URLs should work on the new domain.</li>
						<li>Preset XP values are now the ones provided in the core book 1.0. Of note, I was way off on everything past level 5, so check your NPCs.</li>
					</ul>
				</li>
				<li>v.0.13 (2018-05-31)<br />
					<ul>
						<li>Adjusted canvas dimensions for image export. This might break some NPCs; let me know if your image is cut off.</li>
					</ul>
				</li>
				<li>v.0.12 (2018-03-24)<br />
					<ul>
						<li>The Abilities block no longer displays on the image output for NPCs that don't have any (e.g. Rikkisi).</li>
					</ul>
				</li>
				<!--
				Welcome to the archived changelog!
				<li>v.0.11 (2018-02-24)<br />
					<ul>
						<li>Integrated a DL scaling tool</li>
						<li>Adjusted field sizes and moved footnotes to page foot</li>
					</ul>
				</li>
				<li>v.0.10 (2018-02-18)<br />
					<ul>
						<li>Remembered to upload the proper fonts to the beta copy</li>
					</ul>
				</li>
				<li>v.0.9 (2018-01-29)<br />
					<ul>
						<li>Export to image</li>
						<li>Changelog</li>
						<li>Character encoding in export links</li>
					</ul>
				</li>
				<li>v.0.8 (2018-01-28)<br />
					<ul>
						<li>Export URL</li>
						<li>Responsive design</li>
						<li>Proper fonts</li>
					</ul>
				</li>
				-->
			</ul>
		</details>
	</div>
