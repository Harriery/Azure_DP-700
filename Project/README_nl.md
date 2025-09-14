#  Projectverhaal – Van Ruwe Taxigegevens naar Inzichten

###   Duur: 2 weken

###  Team: Yasin (Teamleider), Kahraman, Emine, Ayşe

###  Tools: Microsoft Fabric | Eventstream | Delta Lake | Dataflow Gen2 | Power BI | Eventhouse | Activator | Trello

## Azure DP-700 Project
Het doel van dit project was om mijn data-engineeringvaardigheden te laten zien in het kader van het Microsoft Fabric DP-700 examen. In dit project heb ik gewerkt aan:

Het verzamelen, laden en transformeren van data (batch & streaming),

Data-transformaties met SQL, PySpark en KQL,

Het ontwerpen van data-architectuur (Lakehouse en Data Warehouse),

Beveiliging, toegangsbeheer en data-governance,

Automatisering met pipelines en triggers,

Monitoring en prestatie-optimalisatie.

Met dit project wilde ik mijn technische kennis en praktijkervaring in Azure/Microsoft Fabric aantonen.

##  Projectdiagram
We zijn begonnen met een diagram om de stappen van het project duidelijk te visualiseren.

<p align="center"> <img src="./images/diyagram.png" alt="diagram" width="600"/> </p>


##  Taakverdeling

Als teamleider gebruikte ik Trello om taken toe te wijzen en sprints te plannen.
Hierdoor konden we het project op tijd afronden.

<p align="center"> <img src="./images/trello.png" alt="trello" width="600"/> </p>

##  Stap 1: Ruwe gegevens ontvangen via Eventstream (real-time)

We hebben taxigegevens in real-time ontvangen via Eventstream.
Elke keer als een taxi een actie deed (zoals rit begonnen of rit beëindigd), kwam er een gebeurtenis binnen.
Deze data werd onbewerkt opgeslagen in de Bronze-laag van Delta Lake.

<p align="center"> <img src="./images/get_eventdata.png" alt="eventdata" width="500"/> </p>


##  Stap 2: Opschonen en transformeren met PySpark
Voor we naar de Silver-laag gingen, maakten we de data schoon met PySpark in een notebook.

<p align="center"> <img src="./images/pyspark.png" alt="pyspark" width="500"/> </p>

We hebben:

Ongeldige of onvolledige rijen verwijderd

Datum/tijd-formats aangepast

Alleen nuttige kolommen geselecteerd

##  Stap 3: Modelleren in Gold-laag met Stermodel
Met Dataflow Gen2 hebben we de Silver-data gemodelleerd in de Gold-laag met een ster-schema (star schema).

<div style="display: flex; gap: 10px; justify-content: center;"> <img src="./images/fact-dim-tables.png" alt="dim" width="450"/> <img src="./images/star-schema.png" alt="schema" width="300"/> </div>

Voorbeeldtabellen: dim_date2, dim_location, dim_vendor, fact_trip

##  Stap 4: Analyse & Visualisatie met Power BI

We verbonden de Gold-data met Power BI en maakten een interactief dashboard.

<div style="display: flex; gap: 10px; justify-content: center;"> <img src="./images/chart1.png" alt="chart1" width="250"/> <img src="./images/chart2.png" alt="chart2" width="250"/> <img src="./images/chart3.png" alt="chart3" width="250"/> </div>

### Dashboard-functies:

Filters per datum, locatie en taxi

KPI's zoals totaal aantal ritten, gemiddelde afstand

Kaarten met locatie-inzichten

##  Stap 5: Real-time monitoring met Eventhouse

We gebruikten Eventhouse om de live datastroom te monitoren.

<div style="display: flex; gap: 10px; justify-content: center;"> <img src="./images/realtime-dashboard1.png" alt="realtime1" width="400"/> <img src="./images/realtime-dashboard2.png" alt="realtime2" width="400"/> </div>

Zo konden we eventuele vertragingen of problemen direct zien.

##  Stap 6: Automatisering met Activator
Met Activator maakten we automatische meldingen, bijvoorbeeld:

Waarschuwing als een taxi meer dan 5 ritten per uur voltooit

Extra controle voor lange ritten 's nachts

Zo konden we niet alleen rapporteren, maar ook proactief reageren op dat

## Licentie
Dit project is gelicentieerd onder de MIT-licentie. Zie het [LICENSE](LICENSE) bestand voor meer informatie.

