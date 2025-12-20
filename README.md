<html lang="de">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Word-Scrambler</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;600&family=IBM+Plex+Mono:wght@300;600&family=JetBrains+Mono:wght@300;600&family=Space+Grotesk:wght@300;600&display=swap" rel="stylesheet">

<style>
:root{
  --font: Inter;
  --size: 16px;
  --theme: 0;

  /* NEW: dropdown base colors */
  --base-bg: #000000;
  --base-fg: #f2f5f4;

  /* NEW: theme-mixed live colors */
  --bg-color: #000000;
  --fg-color: #f2f5f4;

  /* derived */
  --line: rgba(242,245,244,.18);
  --muted: rgba(242,245,244,.72);
  --field-border: rgba(242,245,244,.25);
  --panel: rgba(242,245,244,.03);

  --accent: #42e8ff;
  --matrix-green: #00ff6a;
}

*{ box-sizing:border-box; }

html, body{
  height:100%;
  margin:0;
  background: var(--bg-color);
  color: var(--fg-color);
  font-family: var(--font), system-ui, sans-serif;
}

.app{
  height:100vh;
  display:grid;
  grid-template-columns: 1fr 2fr;
}

/* ===== LEFT ===== */
.left{
  border-right:1px solid var(--line);
  display:flex;
  flex-direction:column;
  min-width: 320px;
}

.topbar{
  padding:14px;
  border-bottom:1px solid var(--line);
}

.settings{
  padding:16px;
  overflow:auto;
}

.section{
  margin-bottom:18px;
}
.section-title{
  font-size:11px;
  text-transform:uppercase;
  letter-spacing:.25em;
  color:var(--muted);
  margin-bottom:8px;
}

label{
  display:block;
  margin-top:10px;
  font-size:12px;
  text-transform:uppercase;
  letter-spacing:.2em;
  color:var(--muted);
}

select, input[type="range"], button.reset, button.export{
  width:100%;
  margin-top:6px;
  background: rgba(0,0,0,.15);
  color: var(--fg-color);
  border:1px solid var(--field-border);
  padding:8px;
  font-size:12px;
}

input[type="range"]{ accent-color: var(--accent); }

.checkbox{
  margin-top:10px;
  display:flex;
  align-items:center;
  gap:10px;
  font-size:12px;
  text-transform:uppercase;
  letter-spacing:.2em;
  color:var(--muted);
}

button.reset, button.export{
  cursor:pointer;
}
button.reset:hover, button.export:hover{ border-color: var(--accent); }

/* NEW: dropdowns side-by-side */
.row{
  display:flex;
  gap:10px;
}
.row > .col{
  flex:1;
  min-width:0;
}

/* ===== RIGHT ===== */
.right{
  height:100vh;
  display:grid;
  grid-template-rows:1fr 1fr;
}

.output{
  overflow:auto;
  padding:18px;
  background: var(--panel);
  font-family: var(--font), system-ui, sans-serif;
  font-size: var(--size);
  letter-spacing:.14em;
  line-height:1.6;
  white-space:pre-wrap;
  border-bottom:1px solid var(--line);
  color: var(--fg-color);
}

textarea{
  width:100%;
  height:100%;
  resize:none;
  border:none;
  outline:none;
  padding:14px;
  background: rgba(0,0,0,.15);
  color: var(--fg-color);
  font-family: var(--font), system-ui, sans-serif;
  font-size:14px;
  letter-spacing:.12em;
}

.char{
  display:inline-block;
  will-change: contents, font-family, color, font-size, vertical-align, opacity;
}

/* ===== EXPORT OVERLAY (foreground window) ===== */
#exportOverlay{
  position:fixed;
  inset:0;
  display:none;
  align-items:center;
  justify-content:center;
  z-index:999999;
  background: rgba(0,0,0,.55);
  backdrop-filter: blur(6px);
}

#exportModal{
  min-width: 320px;
  padding: 22px 20px;
  border:1px solid rgba(255,255,255,.18);
  background: rgba(15,15,15,.92);
  color: #f2f5f4;
  font-family: Inter, system-ui, sans-serif;
  letter-spacing:.12em;
  text-transform: uppercase;
}

#exportPct{
  font-size: 28px;
  margin-bottom: 14px;
}

#exportBar{
  height: 10px;
  background: rgba(255,255,255,.12);
  border-radius: 999px;
  overflow:hidden;
}

#exportBarInner{
  height:100%;
  width:0%;
  background: #75c0c7;
}
</style>
</head>

<body>
<div id="exportOverlay" aria-hidden="true">
  <div id="exportModal" role="dialog" aria-modal="true">
    <div id="exportPct">0%</div>
    <div id="exportBar"><div id="exportBarInner"></div></div>
  </div>
</div>

<div class="app">

<div class="left">

  <div class="topbar">
    <label data-i18n="lblLanguage"></label>
    <select id="languageSelect">
      <option value="de">Deutsch</option>
      <option value="en">English</option>
      <option value="fr">Français</option>
      <option value="es">Español</option>
      <option value="it">Italiano</option>
      <option value="nl">Nederlands</option>
      <option value="pl">Polski</option>
      <option value="pt">Português</option>
      <option value="sv">Svenska</option>
      <option value="fi">Suomi</option>
      <option value="cs">Čeština</option>
      <option value="ro">Română</option>
      <option value="bg">Български</option>
      <option value="ru">Русский</option>
      <option value="zh">中文（简体）</option>
      <option value="hi">हिन्दी</option>
      <option value="ja">日本語</option>
    </select>
  </div>

  <div class="settings">

    <!-- NEW: COLORS -->
    <div class="section">
<div class="section-title" data-i18n="secColors"></div>


      <div class="row">
        <div class="col">
<label data-i18n="lblBackground"></label>
<select id="bgSelect">
  <option value="#000000" data-label-key="bgBlack"></option>
  <option value="#161618" data-label-key="bgTrueGray"></option>
  <option value="#212121" data-label-key="bgAlt1"></option>
  <option value="#2a2e31" data-label-key="bgAlt2"></option>
</select>

        </div>
        <div class="col">
<label data-i18n="lblForeground"></label>
<select id="fgSelect">
  <option value="#888888" data-label-key="fgMuted"></option>
  <option value="#f2f5f4" data-label-key="fgText" selected></option>
  <option value="#75c0c7" data-label-key="fgLink"></option>
  <option value="#008fff" data-label-key="fgEpicBlue"></option>
  <option value="#1db992" data-label-key="fgSoftGreen"></option>
  <option value="#bfbc06" data-label-key="fgOlive"></option>
  <option value="#f7c516" data-label-key="fgYellow"></option>
  <option value="#fc8833" data-label-key="fgAlert"></option>
  <option value="#ee4d2e" data-label-key="fgOrange"></option>
  <option value="#d23c22" data-label-key="fgLogoOrange"></option>
  <option value="#ff0082" data-label-key="fgLegacyPink"></option>
</select>
        </div>
      </div>
    </div>

    <div class="section">
      <div class="section-title" data-i18n="secText"></div>

<select id="fontSelect">
  <!-- Webfonts -->
  <option value="Inter">Inter</option>
  <option value="Space Grotesk">Space Grotesk</option>
  <option value="IBM Plex Mono">IBM Plex Mono</option>
  <option value="JetBrains Mono">JetBrains Mono</option>

  <!-- System Sans -->
  <option value="Arial">Arial</option>
  <option value="Calibri">Calibri (Windows)</option>
  <option value="Segoe UI">Segoe UI (Windows)</option>
  <option value="Helvetica">Helvetica (macOS)</option>

  <!-- System Serif -->
  <option value="Times New Roman">Times New Roman</option>
  <option value="Georgia">Georgia</option>
</select>


      <label data-i18n="lblBaseSize"></label>
      <input id="size" type="range" min="12" max="42" step="1" value="16">
    </div>

    <div class="section">
      <div class="section-title" data-i18n="secScramble"></div>

      <label id="speedLabel"></label>
      <!-- FIX: default = 6 -->
      <input id="speed" type="range" min="0.5" max="40" step="0.5" value="6">

      <label id="scrambleSizeLabel"></label>
      <input id="scrambleSize" type="range" min="0.6" max="1.8" step="0.05" value="1">

      <label id="randomTimeLabel"></label>
      <input id="randomTime" type="range" min="0" max="2" step="0.1" value="0">

      <div class="checkbox">
        <input id="colorToggle" type="checkbox">
        <span data-i18n="optColors"></span>
      </div>

      <div class="checkbox">
        <input id="superscriptToggle" type="checkbox">
        <span data-i18n="optSup"></span>
      </div>

      <div class="checkbox">
        <input id="sequentialToggle" type="checkbox">
        <span data-i18n="optSeqPaste"></span>
      </div>

      <div class="checkbox">
  <input id="matrixToggle" type="checkbox">
  <span data-i18n="optMatrix"></span>

  <input id="matrixGreenToggle" type="checkbox" style="margin-left:12px">
<span data-i18n="optMatrixGreen"></span>

</div>

    </div>

    <div class="section">
      <div class="section-title" data-i18n="secTheme"></div>
      <label data-i18n="lblTheme"></label>
      <input id="themeSlider" type="range" min="0" max="1" step="0.01" value="0">
    </div>

    <!-- NEW: EXPORT -->
<div class="section">
<div class="section-title" data-i18n="secExport"></div>


<div class="row">
  <button id="exportVp8" class="export" type="button">WebM (VP8)</button>
  <button id="exportVp9" class="export" type="button">WebM (VP9)</button>
</div>
</div>


    <div class="section">
      <div class="section-title" data-i18n="secReset"></div>
      <button id="resetBtn" class="reset" data-i18n="resetBtn"></button>
    </div>

  </div>
</div>

<div class="right">
  <div class="output" id="output"></div>
  <textarea id="input"></textarea>
</div>

</div>

<script>
/* ================= FULL I18N ================= */
const I18N = {
  de:{
    htmlLang:"de", title:"Realtime Glyph Scramble", placeholder:"TEXT EINGEBEN…",
    lblLanguage:"Sprache", secText:"Text", lblFont:"Schriftart", lblBaseSize:"Grundgröße",
    secScramble:"Scramble", speed:"Geschwindigkeit (Iterationen / Sekunde)",
    scrambleSize:"Scramble-Größenfaktor", randomTime:"Zusätzliche zufällige Scramble-Zeit",
    optColors:"Zufällige Scramble-Farben", optSup:"Scramble hochgestellt",
    optSeqPaste:"Sequenziell beim Einfügen", optMatrix:"Matrix-Modus",
    secTheme:"Darstellung", lblTheme:"Dunkel ↔ Hell",
    secReset:"Aktionen", resetBtn:"Alles löschen",
    secColors:"Farben",
lblBackground:"Hintergrund",
lblForeground:"Schrift",
optMatrixGreen:"Grün",
secExport:"Export",
bgBlack:"Schwarz",
bgTrueGray:"Richtiges Grau",
bgAlt1:"Alternativer Hintergrund",
bgAlt2:"Alternativer Hintergrund 2",

fgMuted:"Schrift (ausgegraut)",
fgText:"Schrift",
fgLink:"Link",
fgEpicBlue:"Episches Blau",
fgSoftGreen:"Angenehmes Grün",
fgOlive:"Olivgrün des Friedens",
fgYellow:"Gelb",
fgAlert:"Warnfarbe",
fgOrange:"Orange",
fgLogoOrange:"Logo-Orange",
fgLegacyPink:"Altes pr0gramm",


  },
  en:{
    htmlLang:"en", title:"Realtime Glyph Scramble", placeholder:"TYPE TEXT…",
    lblLanguage:"Language", secText:"Text", lblFont:"Font", lblBaseSize:"Base Size",
    secScramble:"Scramble", speed:"Speed (Iterations / Second)",
    scrambleSize:"Scramble Size Multiplier", randomTime:"Additional Random Scramble Time",
    optColors:"Random Scramble Colors", optSup:"Superscript Scramble",
    optSeqPaste:"Sequential on Paste", optMatrix:"Matrix Mode",
    secTheme:"Appearance", lblTheme:"Dark ↔ Light",
    secReset:"Actions", resetBtn:"Reset",
    secColors:"Colors",
lblBackground:"Background",
lblForeground:"Text",
optMatrixGreen:"Green",
secExport:"Export",
bgBlack:"Black",
bgTrueGray:"True Gray",
bgAlt1:"Alternative Background",
bgAlt2:"Alternative Background 2",

fgMuted:"Muted Text",
fgText:"Text",
fgLink:"Link",
fgEpicBlue:"Epic Blue",
fgSoftGreen:"Soft Green",
fgOlive:"Olive Green",
fgYellow:"Yellow",
fgAlert:"Alert Color",
fgOrange:"Orange",
fgLogoOrange:"Logo Orange",
fgLegacyPink:"Legacy Pink",


  },
  fr:{
    htmlLang:"fr", title:"Scramble de glyphes en temps réel", placeholder:"SAISIR DU TEXTE…",
    lblLanguage:"Langue", secText:"Texte", lblFont:"Police", lblBaseSize:"Taille de base",
    secScramble:"Scramble", speed:"Vitesse (itérations / seconde)",
    scrambleSize:"Facteur de taille du scramble", randomTime:"Durée aléatoire du scramble",
    optColors:"Couleurs aléatoires", optSup:"Exposant",
    optSeqPaste:"Séquentiel au collage", optMatrix:"Mode Matrix",
    secTheme:"Apparence", lblTheme:"Sombre ↔ Clair",
    secReset:"Actions", resetBtn:"Réinitialiser",
    secColors:"Couleurs",
lblBackground:"Arrière-plan",
lblForeground:"Texte",
optMatrixGreen:"Vert",
secExport:"Export",
bgBlack:"Noir",
bgTrueGray:"Gris réel",
bgAlt1:"Arrière-plan alternatif",
bgAlt2:"Arrière-plan alternatif 2",

fgMuted:"Texte atténué",
fgText:"Texte",
fgLink:"Lien",
fgEpicBlue:"Bleu épique",
fgSoftGreen:"Vert doux",
fgOlive:"Vert olive",
fgYellow:"Jaune",
fgAlert:"Couleur d’alerte",
fgOrange:"Orange",
fgLogoOrange:"Orange du logo",
fgLegacyPink:"Rose ancien",


  },
  es:{
    htmlLang:"es", title:"Scramble de glifos en tiempo real", placeholder:"ESCRIBE TEXTO…",
    lblLanguage:"Idioma", secText:"Texto", lblFont:"Fuente", lblBaseSize:"Tamaño base",
    secScramble:"Scramble", speed:"Velocidad (iteraciones / segundo)",
    scrambleSize:"Multiplicador de tamaño del scramble", randomTime:"Tiempo aleatorio adicional",
    optColors:"Colores aleatorios", optSup:"Superíndice",
    optSeqPaste:"Secuencial al pegar", optMatrix:"Modo Matrix",
    secTheme:"Apariencia", lblTheme:"Oscuro ↔ Claro",
    secReset:"Acciones", resetBtn:"Restablecer",
    secColors:"Colores",
lblBackground:"Fondo",
lblForeground:"Texto",
optMatrixGreen:"Verde",
secExport:"Exportar",
bgBlack:"Negro",
bgTrueGray:"Gris real",
bgAlt1:"Fondo alternativo",
bgAlt2:"Fondo alternativo 2",

fgMuted:"Texto atenuado",
fgText:"Texto",
fgLink:"Enlace",
fgEpicBlue:"Azul épico",
fgSoftGreen:"Verde suave",
fgOlive:"Verde oliva",
fgYellow:"Amarillo",
fgAlert:"Color de alerta",
fgOrange:"Naranja",
fgLogoOrange:"Naranja del logo",
fgLegacyPink:"Rosa antiguo",


  },
  it:{
    htmlLang:"it", title:"Scramble di glifi in tempo reale", placeholder:"INSERISCI TESTO…",
    lblLanguage:"Lingua", secText:"Testo", lblFont:"Carattere", lblBaseSize:"Dimensione base",
    secScramble:"Scramble", speed:"Velocità (iterazioni / secondo)",
    scrambleSize:"Moltiplicatore dimensione scramble", randomTime:"Durata casuale aggiuntiva",
    optColors:"Colori casuali", optSup:"Apice",
    optSeqPaste:"Sequenziale all'incolla", optMatrix:"Modalità Matrix",
    secTheme:"Aspetto", lblTheme:"Scuro ↔ Chiaro",
    secReset:"Azioni", resetBtn:"Reimposta",
    secColors:"Colori",
lblBackground:"Sfondo",
lblForeground:"Testo",
optMatrixGreen:"Verde",
secExport:"Esporta",
bgBlack:"Nero",
bgTrueGray:"Grigio reale",
bgAlt1:"Sfondo alternativo",
bgAlt2:"Sfondo alternativo 2",

fgMuted:"Testo attenuato",
fgText:"Testo",
fgLink:"Link",
fgEpicBlue:"Blu epico",
fgSoftGreen:"Verde tenue",
fgOlive:"Verde oliva",
fgYellow:"Giallo",
fgAlert:"Colore di avviso",
fgOrange:"Arancione",
fgLogoOrange:"Arancione logo",
fgLegacyPink:"Rosa storico",


  },
  nl:{
    htmlLang:"nl", title:"Realtime Glyph Scramble", placeholder:"TEKST INVOEREN…",
    lblLanguage:"Taal", secText:"Tekst", lblFont:"Lettertype", lblBaseSize:"Basisgrootte",
    secScramble:"Scramble", speed:"Snelheid (iteraties / seconde)",
    scrambleSize:"Scramble-groottefactor", randomTime:"Extra willekeurige tijd",
    optColors:"Willekeurige kleuren", optSup:"Superscript",
    optSeqPaste:"Sequentieel bij plakken", optMatrix:"Matrix-modus",
    secTheme:"Weergave", lblTheme:"Donker ↔ Licht",
    secReset:"Acties", resetBtn:"Reset",
    secColors:"Kleuren",
lblBackground:"Achtergrond",
lblForeground:"Tekst",
optMatrixGreen:"Groen",
secExport:"Exporteren",
bgBlack:"Zwart",
bgTrueGray:"Echt grijs",
bgAlt1:"Alternatieve achtergrond",
bgAlt2:"Alternatieve achtergrond 2",

fgMuted:"Gedempte tekst",
fgText:"Tekst",
fgLink:"Link",
fgEpicBlue:"Episch blauw",
fgSoftGreen:"Zacht groen",
fgOlive:"Olijfgroen",
fgYellow:"Geel",
fgAlert:"Waarschuwingskleur",
fgOrange:"Oranje",
fgLogoOrange:"Logo-oranje",
fgLegacyPink:"Oud roze",


  },
  pl:{
    htmlLang:"pl", title:"Scramble glifów w czasie rzeczywistym", placeholder:"WPISZ TEKST…",
    lblLanguage:"Język", secText:"Tekst", lblFont:"Czcionka", lblBaseSize:"Rozmiar bazowy",
    secScramble:"Scramble", speed:"Prędkość (iteracje / sekundę)",
    scrambleSize:"Mnożnik rozmiaru scramble", randomTime:"Dodatkowy losowy czas",
    optColors:"Losowe kolory", optSup:"Indeks górny",
    optSeqPaste:"Sekwencyjnie przy wklejaniu", optMatrix:"Tryb Matrix",
    secTheme:"Wygląd", lblTheme:"Ciemny ↔ Jasny",
    secReset:"Akcje", resetBtn:"Wyczyść",
    secColors:"Kolory",
lblBackground:"Tło",
lblForeground:"Tekst",
optMatrixGreen:"Zielony",
secExport:"Eksport",
bgBlack:"Czarny",
bgTrueGray:"Prawdziwa szarość",
bgAlt1:"Alternatywne tło",
bgAlt2:"Alternatywne tło 2",

fgMuted:"Przygaszony tekst",
fgText:"Tekst",
fgLink:"Link",
fgEpicBlue:"Epicki niebieski",
fgSoftGreen:"Delikatna zieleń",
fgOlive:"Oliwkowy",
fgYellow:"Żółty",
fgAlert:"Kolor ostrzegawczy",
fgOrange:"Pomarańczowy",
fgLogoOrange:"Pomarańczowy logo",
fgLegacyPink:"Stary róż",


  },
  pt:{
    htmlLang:"pt", title:"Scramble de glifos em tempo real", placeholder:"DIGITE TEXTO…",
    lblLanguage:"Idioma", secText:"Texto", lblFont:"Fonte", lblBaseSize:"Tamanho base",
    secScramble:"Scramble", speed:"Velocidade (iterações / segundo)",
    scrambleSize:"Multiplicador de tamanho", randomTime:"Tempo aleatório adicional",
    optColors:"Cores aleatórias", optSup:"Sobrescrito",
    optSeqPaste:"Sequencial ao colar", optMatrix:"Modo Matrix",
    secTheme:"Aparência", lblTheme:"Escuro ↔ Claro",
    secReset:"Ações", resetBtn:"Redefinir",
    secColors:"Cores",
lblBackground:"Fundo",
lblForeground:"Texto",
optMatrixGreen:"Verde",
secExport:"Exportar",
bgBlack:"Preto",
bgTrueGray:"Cinza real",
bgAlt1:"Fundo alternativo",
bgAlt2:"Fundo alternativo 2",

fgMuted:"Texto suave",
fgText:"Texto",
fgLink:"Link",
fgEpicBlue:"Azul épico",
fgSoftGreen:"Verde suave",
fgOlive:"Verde oliva",
fgYellow:"Amarelo",
fgAlert:"Cor de alerta",
fgOrange:"Laranja",
fgLogoOrange:"Laranja do logo",
fgLegacyPink:"Rosa antigo",


  },
  sv:{
    htmlLang:"sv", title:"Realtids-glyph-scramble", placeholder:"SKRIV TEXT…",
    lblLanguage:"Språk", secText:"Text", lblFont:"Typsnitt", lblBaseSize:"Basstorlek",
    secScramble:"Scramble", speed:"Hastighet (iterationer / sekund)",
    scrambleSize:"Scramble-storleksfaktor", randomTime:"Extra slumpmässig tid",
    optColors:"Slumpfärger", optSup:"Upphöjd",
    optSeqPaste:"Sekventiellt vid inklistring", optMatrix:"Matrix-läge",
    secTheme:"Utseende", lblTheme:"Mörk ↔ Ljus",
    secReset:"Åtgärder", resetBtn:"Rensa",
    secColors:"Färger",
lblBackground:"Bakgrund",
lblForeground:"Text",
optMatrixGreen:"Grön",
secExport:"Export",
bgBlack:"Svart",
bgTrueGray:"Äkta grå",
bgAlt1:"Alternativ bakgrund",
bgAlt2:"Alternativ bakgrund 2",

fgMuted:"Dämpad text",
fgText:"Text",
fgLink:"Länk",
fgEpicBlue:"Episk blå",
fgSoftGreen:"Mjuk grön",
fgOlive:"Olivgrön",
fgYellow:"Gul",
fgAlert:"Varningsfärg",
fgOrange:"Orange",
fgLogoOrange:"Logotyp-orange",
fgLegacyPink:"Gammal rosa",


  },
  fi:{
    htmlLang:"fi", title:"Reaaliaikainen glyyfi-sekoitus", placeholder:"KIRJOITA TEKSTI…",
    lblLanguage:"Kieli", secText:"Teksti", lblFont:"Fontti", lblBaseSize:"Peruskoko",
    secScramble:"Sekoittaminen", speed:"Nopeus (iteraatiota / sekunti)",
    scrambleSize:"Kokokerroin", randomTime:"Lisäaika satunnaisesti",
    optColors:"Satunnaisvärit", optSup:"Yläindeksi",
    optSeqPaste:"Peräkkäinen liittäminen", optMatrix:"Matrix-tila",
    secTheme:"Ulkoasu", lblTheme:"Tumma ↔ Vaalea",
    secReset:"Toiminnot", resetBtn:"Tyhjennä",
    secColors:"Värit",
lblBackground:"Tausta",
lblForeground:"Teksti",
optMatrixGreen:"Vihreä",
secExport:"Vienti",
bgBlack:"Musta",
bgTrueGray:"Aito harmaa",
bgAlt1:"Vaihtoehtoinen tausta",
bgAlt2:"Vaihtoehtoinen tausta 2",

fgMuted:"Häivytetty teksti",
fgText:"Teksti",
fgLink:"Linkki",
fgEpicBlue:"Eeppinen sininen",
fgSoftGreen:"Pehmeä vihreä",
fgOlive:"Oliivinvihreä",
fgYellow:"Keltainen",
fgAlert:"Varoitusväri",
fgOrange:"Oranssi",
fgLogoOrange:"Logon oranssi",
fgLegacyPink:"Vanha pinkki",


  },
  cs:{
    htmlLang:"cs", title:"Scramble glyfů v reálném čase", placeholder:"ZADAT TEXT…",
    lblLanguage:"Jazyk", secText:"Text", lblFont:"Písmo", lblBaseSize:"Základní velikost",
    secScramble:"Scramble", speed:"Rychlost (iterace / sekundu)",
    scrambleSize:"Násobič velikosti", randomTime:"Náhodná dodatečná doba",
    optColors:"Náhodné barvy", optSup:"Horní index",
    optSeqPaste:"Sekvenčně při vložení", optMatrix:"Matrix režim",
    secTheme:"Vzhled", lblTheme:"Tmavý ↔ Světlý",
    secReset:"Akce", resetBtn:"Vymazat",
    secColors:"Barvy",
lblBackground:"Pozadí",
lblForeground:"Text",
optMatrixGreen:"Zelená",
secExport:"Export",
bgBlack:"Černá",
bgTrueGray:"Skutečná šedá",
bgAlt1:"Alternativní pozadí",
bgAlt2:"Alternativní pozadí 2",

fgMuted:"Tlumený text",
fgText:"Text",
fgLink:"Odkaz",
fgEpicBlue:"Epická modrá",
fgSoftGreen:"Jemná zelená",
fgOlive:"Olivová",
fgYellow:"Žlutá",
fgAlert:"Výstražná barva",
fgOrange:"Oranžová",
fgLogoOrange:"Logo oranžová",
fgLegacyPink:"Starorůžová",


  },
  ro:{
    htmlLang:"ro", title:"Scramble de glife în timp real", placeholder:"INTRODU TEXT…",
    lblLanguage:"Limbă", secText:"Text", lblFont:"Font", lblBaseSize:"Dimensiune de bază",
    secScramble:"Scramble", speed:"Viteză (iterații / secundă)",
    scrambleSize:"Multiplicator dimensiune", randomTime:"Timp suplimentar aleator",
    optColors:"Culori aleatorii", optSup:"Exponent",
    optSeqPaste:"Secvențial la lipire", optMatrix:"Mod Matrix",
    secTheme:"Aspect", lblTheme:"Întunecat ↔ Deschis",
    secReset:"Acțiuni", resetBtn:"Resetează",
    secColors:"Culori",
lblBackground:"Fundal",
lblForeground:"Text",
optMatrixGreen:"Verde",
secExport:"Export",
bgBlack:"Negru",
bgTrueGray:"Gri real",
bgAlt1:"Fundal alternativ",
bgAlt2:"Fundal alternativ 2",

fgMuted:"Text estompat",
fgText:"Text",
fgLink:"Link",
fgEpicBlue:"Albastru epic",
fgSoftGreen:"Verde moale",
fgOlive:"Verde măsliniu",
fgYellow:"Galben",
fgAlert:"Culoare de alertă",
fgOrange:"Portocaliu",
fgLogoOrange:"Portocaliu logo",
fgLegacyPink:"Roz vechi",


  },
  bg:{
    htmlLang:"bg", title:"Скрамблиране на глифове в реално време", placeholder:"ВЪВЕДЕТЕ ТЕКСТ…",
    lblLanguage:"Език", secText:"Текст", lblFont:"Шрифт", lblBaseSize:"Базов размер",
    secScramble:"Скрамбъл", speed:"Скорост (итерации / секунда)",
    scrambleSize:"Множител на размера", randomTime:"Допълнително случайно време",
    optColors:"Случайни цветове", optSup:"Горен индекс",
    optSeqPaste:"Последователно при поставяне", optMatrix:"Matrix режим",
    secTheme:"Външен вид", lblTheme:"Тъмен ↔ Светъл",
    secReset:"Действия", resetBtn:"Изчисти",
    secColors:"Цветове",
lblBackground:"Фон",
lblForeground:"Текст",
optMatrixGreen:"Зелено",
secExport:"Експорт",
bgBlack:"Черно",
bgTrueGray:"Истинско сиво",
bgAlt1:"Алтернативен фон",
bgAlt2:"Алтернативен фон 2",

fgMuted:"Приглушен текст",
fgText:"Текст",
fgLink:"Връзка",
fgEpicBlue:"Епично синьо",
fgSoftGreen:"Меко зелено",
fgOlive:"Маслинено зелено",
fgYellow:"Жълто",
fgAlert:"Предупредителен цвят",
fgOrange:"Оранжево",
fgLogoOrange:"Лого оранжево",
fgLegacyPink:"Старо розово",


  },
  ru:{
    htmlLang:"ru", title:"Скремблирование глифов в реальном времени", placeholder:"ВВЕДИТЕ ТЕКСТ…",
    lblLanguage:"Язык", secText:"Текст", lblFont:"Шрифт", lblBaseSize:"Базовый размер",
    secScramble:"Scramble", speed:"Скорость (итераций / сек.)",
    scrambleSize:"Множитель размера", randomTime:"Дополнительное случайное время",
    optColors:"Случайные цвета", optSup:"Верхний индекс",
    optSeqPaste:"Последовательно при вставке", optMatrix:"Режим Matrix",
    secTheme:"Внешний вид", lblTheme:"Тёмный ↔ Светлый",
    secReset:"Действия", resetBtn:"Очистить",
    secColors:"Цвета",
lblBackground:"Фон",
lblForeground:"Текст",
optMatrixGreen:"Зелёный",
secExport:"Экспорт",
bgBlack:"Чёрный",
bgTrueGray:"Настоящий серый",
bgAlt1:"Альтернативный фон",
bgAlt2:"Альтернативный фон 2",

fgMuted:"Приглушённый текст",
fgText:"Текст",
fgLink:"Ссылка",
fgEpicBlue:"Эпический синий",
fgSoftGreen:"Мягкий зелёный",
fgOlive:"Оливковый",
fgYellow:"Жёлтый",
fgAlert:"Цвет предупреждения",
fgOrange:"Оранжевый",
fgLogoOrange:"Логотип оранжевый",
fgLegacyPink:"Старый розовый",


  },
  zh:{
    htmlLang:"zh", title:"实时字形扰乱", placeholder:"输入文本…",
    lblLanguage:"语言", secText:"文本", lblFont:"字体", lblBaseSize:"基础字号",
    secScramble:"扰乱", speed:"速度（次/秒）",
    scrambleSize:"扰乱大小倍率", randomTime:"额外随机扰乱时间",
    optColors:"随机颜色", optSup:"上标",
    optSeqPaste:"粘贴时顺序扰乱", optMatrix:"矩阵模式",
    secTheme:"外观", lblTheme:"暗 ↔ 亮",
    secReset:"操作", resetBtn:"清空",
    secColors:"颜色",
lblBackground:"背景",
lblForeground:"文字",
optMatrixGreen:"绿色",
secExport:"导出",
bgBlack:"黑色",
bgTrueGray:"真实灰色",
bgAlt1:"替代背景",
bgAlt2:"替代背景 2",

fgMuted:"弱化文本",
fgText:"文本",
fgLink:"链接",
fgEpicBlue:"史诗蓝",
fgSoftGreen:"柔和绿",
fgOlive:"橄榄绿",
fgYellow:"黄色",
fgAlert:"警告颜色",
fgOrange:"橙色",
fgLogoOrange:"标志橙",
fgLegacyPink:"旧粉色",


  },
  hi:{
    htmlLang:"hi", title:"रीयलटाइम ग्लिफ़ स्क्रैम्बल", placeholder:"पाठ दर्ज करें…",
    lblLanguage:"भाषा", secText:"पाठ", lblFont:"फ़ॉन्ट", lblBaseSize:"आधार आकार",
    secScramble:"स्क्रैम्बल", speed:"गति (इटरेशन/सेकंड)",
    scrambleSize:"स्क्रैम्बल आकार गुणक", randomTime:"अतिरिक्त यादृच्छिक समय",
    optColors:"यादृच्छिक रंग", optSup:"सुपरस्क्रिप्ट",
    optSeqPaste:"पेस्ट पर क्रमिक", optMatrix:"मैट्रिक्स मोड",
    secTheme:"दिखावट", lblTheme:"डार्क ↔ लाइट",
    secReset:"क्रियाएँ", resetBtn:"सब साफ़ करें",
    secColors:"रंग",
lblBackground:"पृष्ठभूमि",
lblForeground:"पाठ",
optMatrixGreen:"हरा",
secExport:"निर्यात",
bgBlack:"काला",
bgTrueGray:"वास्तविक ग्रे",
bgAlt1:"वैकल्पिक पृष्ठभूमि",
bgAlt2:"वैकल्पिक पृष्ठभूमि 2",

fgMuted:"मंद पाठ",
fgText:"पाठ",
fgLink:"लिंक",
fgEpicBlue:"एपिक नीला",
fgSoftGreen:"हल्का हरा",
fgOlive:"जैतूनी हरा",
fgYellow:"पीला",
fgAlert:"चेतावनी रंग",
fgOrange:"नारंगी",
fgLogoOrange:"लोगो नारंगी",
fgLegacyPink:"पुराना गुलाबी",


  },
  ja:{
    htmlLang:"ja", title:"リアルタイム・グリフスクランブル", placeholder:"テキストを入力…",
    lblLanguage:"言語", secText:"テキスト", lblFont:"フォント", lblBaseSize:"基本サイズ",
    secScramble:"スクランブル", speed:"速度（回/秒）",
    scrambleSize:"スクランブル倍率", randomTime:"追加ランダム時間",
    optColors:"ランダムカラー", optSup:"上付き",
    optSeqPaste:"貼り付け時に順次", optMatrix:"マトリックスモード",
    secTheme:"表示", lblTheme:"ダーク ↔ ライト",
    secReset:"操作", resetBtn:"すべてクリア",
    secColors:"色",
lblBackground:"背景",
lblForeground:"テキスト",
optMatrixGreen:"緑",
secExport:"書き出し",
bgBlack:"黒",
bgTrueGray:"本物のグレー",
bgAlt1:"代替背景",
bgAlt2:"代替背景 2",

fgMuted:"控えめなテキスト",
fgText:"テキスト",
fgLink:"リンク",
fgEpicBlue:"エピックブルー",
fgSoftGreen:"ソフトグリーン",
fgOlive:"オリーブグリーン",
fgYellow:"黄色",
fgAlert:"警告色",
fgOrange:"オレンジ",
fgLogoOrange:"ロゴオレンジ",
fgLegacyPink:"旧ピンク",


  }
};

/* ================= SCRAMBLE ENGINE (ORIGINAL + FIXES) ================= */
const output=document.getElementById("output");
const input=document.getElementById("input");

let glyphs=[];
let speed=6, scrambleSize=1, randomColors=false, superscript=false,
    sequentialPaste=false, matrixMode=false, matrixGreen=false,
    maxExtraTime=0;


const NBSP="\u00A0";
const normalPool="AaBbCcDdEeFfGgHhIiJjKkLlMmNnOoPpQqRrSsTuUvVwWxXyYzZ0123456789!@#$%^&*()-_=+[]{}<>?/\\|~;:,.";
const matrixPool="アイウエオカキクケコサシスセソタチツテトナニヌネノAaBbCcDdEeFfGgHhIiJjKkLlMmNnOoPpQqRrSsTuUvVwWxXyYzZ0123456789!@#$%^&*()-_=+[]{}<>?/\\|~;:,.";
const normalFonts = [
  // Webfonts
  "Inter",
  "Space Grotesk",
  "IBM Plex Mono",
  "JetBrains Mono",

  // System Sans
  "Arial",
  "Calibri",
  "Segoe UI",
  "Helvetica",
  "Verdana",

  // System Serif
  "Times New Roman",
  "Georgia"
];


const matrixFont="IBM Plex Mono, monospace";

function rand(arr){ return arr[Math.random()*arr.length|0]; }
function randColor(){ return `rgb(${Math.random()*255|0},${Math.random()*255|0},${Math.random()*255|0})`; }

class Glyph{
constructor(ch, delay=0, hidden=false){
  this.target = ch;
  this.delay = delay;
  this.hidden = hidden;
  this.locked = false;

  // ✅ NEW: Zeilenumbruch korrekt behandeln
  if(ch === "\n"){
    this.isNewline = true;
    this.span = document.createElement("br");
    output.appendChild(this.span);
    return;
  }

  this.isNewline = false;

  this.span = document.createElement("span");
  this.span.className = "char";
  this.span.textContent = ch === " " ? NBSP : ch;

  if(hidden) this.span.style.opacity = "0";

  output.appendChild(this.span);
  this.run();
}

run(){
  if(this.isNewline) return;

    this.locked=false;
    this._token=(this._token||0)+1;
    const token=this._token;

    const baseIterations=6;
    const intervalForExtra = () => 1000 / Math.max(0.1, speed);
    const extraIterations=maxExtraTime>0
      ? Math.floor(Math.random()*(maxExtraTime*1000/intervalForExtra()))
      : 0;
    const maxIterations=baseIterations+extraIterations;

    const start=()=>{
      let i=0;
      const step=()=>{
        if(token!==this._token) return;
        if(i++>=maxIterations){ this.lock(); return; }

        if(this.hidden){
          this.hidden=false;
          this.span.style.opacity="1";
        }

        this.span.style.fontFamily=matrixMode?matrixFont:rand(normalFonts);
        this.span.style.fontSize=`calc(var(--size)*${scrambleSize})`;
        this.span.style.verticalAlign=superscript?"super":"baseline";
this.span.style.color = matrixMode
  ? (matrixGreen ? "var(--matrix-green)" : "var(--fg-color)")
  : randomColors ? randColor() : "";


        const pool=matrixMode?matrixPool:normalPool;
        this.span.textContent=this.target===" "?NBSP:pool[Math.random()*pool.length|0];

        const liveInterval = 1000 / Math.max(0.1, speed);
        setTimeout(()=>requestAnimationFrame(step), liveInterval);
      };
      requestAnimationFrame(step);
    };

    if(this.delay>0) setTimeout(start,this.delay);
    else start();
  }
lock(){
  if(this.isNewline) return;

    this.span.style.fontFamily="";
    this.span.style.fontSize="";
    this.span.style.verticalAlign="";
    this.span.style.color="";
    this.span.style.opacity="1";
    this.span.textContent=this.target===" "?NBSP:this.target;
    this.locked=true;
  }
  remove(){
    this._token=(this._token||0)+1;
    this.span.remove();
  }
}

function syncToValue(val, delayMap=null, hideMap=null){
  while(glyphs.length>val.length) glyphs.pop().remove();

  for(let i=0;i<val.length;i++){
    const ch=val[i];
    const delay=delayMap?delayMap(i)||0:0;
    const hide=hideMap?!!hideMap(i):false;

    if(!glyphs[i]) glyphs[i]=new Glyph(ch,delay,hide);
    else if(glyphs[i].target!==ch){
      glyphs[i].remove();
      glyphs[i]=new Glyph(ch,delay,hide);
    }else if(delayMap||hideMap){
      glyphs[i].delay=delay;
      glyphs[i].hidden=hide;
      glyphs[i].run();
    }
  }
}

/* INPUT */
input.addEventListener("input",()=>syncToValue(input.value));

input.addEventListener("paste",e=>{
  if(!sequentialPaste) return;
  e.preventDefault();

  const text=e.clipboardData.getData("text");
  const s=input.selectionStart, epos=input.selectionEnd;
  const val=input.value.slice(0,s)+text+input.value.slice(epos);
  input.value=val;
  input.setSelectionRange(s+text.length,s+text.length);

  const delayMap=i=>i>=s&&i<s+text.length?(i-s)*120:0;
  const hideMap=i=>i>=s&&i<s+text.length;

  syncToValue(val,delayMap,hideMap);
});

/* ================= THEME + DERIVED COLORS ================= */
function hexToRgbAny(v){
  const s=(v||"").trim().toLowerCase();
  if(s.startsWith("rgb")){
    const m=s.match(/(\d+)\D+(\d+)\D+(\d+)/);
    if(!m) return {r:255,g:255,b:255};
    return {r:+m[1],g:+m[2],b:+m[3]};
  }
  let x=s.replace("#","");
  if(x.length===3) x=x.split("").map(c=>c+c).join("");
  const n=parseInt(x||"000000",16);
  return {r:(n>>16)&255,g:(n>>8)&255,b:n&255};
}

function rgbToCss({r,g,b}){ return `rgb(${r|0},${g|0},${b|0})`; }

function mixRgb(a,b,t){
  return {
    r: a.r + (b.r-a.r)*t,
    g: a.g + (b.g-a.g)*t,
    b: a.b + (b.b-a.b)*t
  };
}

function applyDerivedFromFg(fg){
  const r=Math.max(0,Math.min(255,fg.r|0));
  const g=Math.max(0,Math.min(255,fg.g|0));
  const b=Math.max(0,Math.min(255,fg.b|0));
  const root=document.documentElement.style;
  root.setProperty("--line", `rgba(${r},${g},${b},.18)`);
  root.setProperty("--muted", `rgba(${r},${g},${b},.72)`);
  root.setProperty("--field-border", `rgba(${r},${g},${b},.25)`);
  root.setProperty("--panel", `rgba(${r},${g},${b},.03)`);
}

function getCssVar(name){
  return getComputedStyle(document.documentElement).getPropertyValue(name).trim();
}

function applyThemeMix(){
  const t = +document.getElementById("themeSlider").value;

  const baseBg = hexToRgbAny(getCssVar("--base-bg"));
  const baseFgRaw = getCssVar("--base-fg").toLowerCase();

  const bg = mixRgb(baseBg, {r:255,g:255,b:255}, t);

  let fg;
  if(baseFgRaw === "#f2f5f4" || baseFgRaw === "#ffffff" || baseFgRaw === "rgb(242, 245, 244)" || baseFgRaw === "rgb(255, 255, 255)"){
    fg = mixRgb({r:255,g:255,b:255}, {r:0,g:0,b:0}, t);
  }else{
    fg = hexToRgbAny(baseFgRaw);
  }

  const root=document.documentElement.style;
  root.setProperty("--bg-color", rgbToCss(bg));
  root.setProperty("--fg-color", rgbToCss(fg));
  applyDerivedFromFg(fg);
}

/* ================= UI ================= */
document.getElementById("fontSelect").onchange=e=>document.documentElement.style.setProperty("--font",e.target.value);
document.getElementById("size").oninput=e=>document.documentElement.style.setProperty("--size",e.target.value+"px");

const speedLabel=document.getElementById("speedLabel");
const scrambleSizeLabel=document.getElementById("scrambleSizeLabel");
const randomTimeLabel=document.getElementById("randomTimeLabel");

document.getElementById("speed").oninput=e=>{
  speed=+e.target.value;
  speedLabel.textContent=`${I18N[cur].speed}: ${speed}`;
};
document.getElementById("scrambleSize").oninput=e=>{
  scrambleSize=+e.target.value;
  scrambleSizeLabel.textContent=`${I18N[cur].scrambleSize}: ${scrambleSize.toFixed(2)}×`;
};
document.getElementById("randomTime").oninput=e=>{
  maxExtraTime=+e.target.value;
  randomTimeLabel.textContent=`${I18N[cur].randomTime}: ${maxExtraTime.toFixed(1)} s`;
};
document.getElementById("colorToggle").onchange=e=>randomColors=e.target.checked;
document.getElementById("superscriptToggle").onchange=e=>superscript=e.target.checked;
document.getElementById("sequentialToggle").onchange=e=>sequentialPaste=e.target.checked;
document.getElementById("matrixToggle").onchange=e=>matrixMode=e.target.checked;
document.getElementById("matrixGreenToggle").onchange =
  e => matrixGreen = e.target.checked;


document.getElementById("themeSlider").oninput=applyThemeMix;

const bgSelect=document.getElementById("bgSelect");
const fgSelect=document.getElementById("fgSelect");

bgSelect.onchange=e=>{
  document.documentElement.style.setProperty("--base-bg", e.target.value);
  applyThemeMix();
};
fgSelect.onchange=e=>{
  document.documentElement.style.setProperty("--base-fg", e.target.value);
  applyThemeMix();
};

/* RESET */
document.getElementById("resetBtn").onclick=()=>{
  input.value="";
  glyphs.forEach(g=>g.remove());
  glyphs=[];
  output.innerHTML="";
};

/* LANGUAGE */
let cur="de";
const langSel=document.getElementById("languageSelect");
langSel.onchange=()=>applyLang(langSel.value);

function applyLang(l){
  cur=l;
  const t=I18N[l]||I18N.de;
  document.documentElement.lang=t.htmlLang||"de";
  document.title=t.title||"Realtime Glyph Scramble";
  input.placeholder=t.placeholder||"TEXT EINGEBEN…";
  document.querySelectorAll("[data-i18n]").forEach(el=>{
el.textContent = t[el.dataset.i18n] ?? `[${el.dataset.i18n}]`;

  });
  speedLabel.textContent=`${t.speed}: ${speed}`;
  scrambleSizeLabel.textContent=`${t.scrambleSize}: ${scrambleSize.toFixed(2)}×`;
  randomTimeLabel.textContent=`${t.randomTime}: ${maxExtraTime.toFixed(1)} s`;
  applySelectI18n(t);

}
applyLang("de");
applyThemeMix();
function applySelectI18n(t){
  document.querySelectorAll("option[data-label-key]").forEach(opt=>{
    const key = opt.dataset.labelKey;
    opt.textContent = t[key] ?? `[${key}]`;
  });
}

/* ================= EXPORT (WebM VP8 / VP9, no audio, FIREFOX-STABLE) ================= */

const exportOverlay=document.getElementById("exportOverlay");
const exportPct=document.getElementById("exportPct");
const exportBarInner=document.getElementById("exportBarInner");

function showExportOverlay(){
  exportPct.textContent="0%";
  exportBarInner.style.width="0%";
  exportOverlay.style.display="flex";
  exportOverlay.setAttribute("aria-hidden","false");
}
function hideExportOverlay(){
  exportOverlay.style.display="none";
  exportOverlay.setAttribute("aria-hidden","true");
}

function parsePx(v){
  const n=parseFloat(String(v||"").replace("px",""));
  return Number.isFinite(n)?n:0;
}

function getExportStyleSnapshot(){
  const root = getComputedStyle(document.documentElement);
  const out  = getComputedStyle(output);

  const width = 720; // ChatGPT-like

  const paddingTop    = parsePx(out.paddingTop);
  const paddingRight  = parsePx(out.paddingRight);
  const paddingBottom = parsePx(out.paddingBottom);
  const paddingLeft   = parsePx(out.paddingLeft);

  const fontSizeBase = parsePx(root.getPropertyValue("--size").trim() || out.fontSize);
  const fontFamilyUI = root.getPropertyValue("--font").trim() || out.fontFamily;

  let lineHeightPx = parsePx(out.lineHeight);
  if(!lineHeightPx){
    const lh = out.lineHeight;
    if(String(lh).includes(".")) lineHeightPx = fontSizeBase * parseFloat(lh);
    else lineHeightPx = fontSizeBase * 1.6;
  }

  const letterSpacingPx = parsePx(out.letterSpacing);

  const bg = root.getPropertyValue("--bg-color").trim() || out.backgroundColor || "#000";
  const fg = root.getPropertyValue("--fg-color").trim() || out.color || "#f2f5f4";
  const matrixGreen = root.getPropertyValue("--matrix-green").trim() || "#00ff6a";

  return {
    width,
    paddingTop, paddingRight, paddingBottom, paddingLeft,
    fontSizeBase,
    lineHeightPx,
    letterSpacingPx,
    fontFamilyUI,
    bg, fg, matrixGreen
  };
}

function makeCanvasFont(fontFamily, sizePx, weight=300){
  const fontStack = [
    fontFamily,

    // Fallbacks (geordnet nach Verfügbarkeit)
    "Inter",
    "Segoe UI",
    "Arial",
    "Calibri",
    "Helvetica",
    "Verdana",
    "Times New Roman",
    "Georgia",

    "system-ui",
    "sans-serif"
  ].join(", ");

  return `${weight} ${sizePx}px ${fontStack}`;
}

function measureTokenWidth(ctx, token, letterSpacingPx){
  let w = 0;
  for(const ch of token){
    const isSpace = ch === " ";
    const glyph = isSpace ? NBSP : ch;
    const gw = ctx.measureText(glyph).width || 0;

    // CSS-korrekt:
    // - normale Zeichen: + letter-spacing
    // - Space: spacing links + rechts
    w += gw + letterSpacingPx * (isSpace ? 2 : 1);
  }
  return w;
}


function tokenizePreserve(text){
  // tokens: "\n" as own token, runs of spaces as token, runs of non-space non-newline as token
  const tokens=[];
  let mode=null; // "word" | "space"
  let buf="";

  const flush=()=>{
    if(buf){ tokens.push(buf); buf=""; }
  };

  for(const ch of text){
    if(ch === "\n"){
      flush();
      tokens.push("\n");
      mode=null;
      continue;
    }
    if(ch === " "){
      if(mode !== "space"){ flush(); mode="space"; }
      buf += " ";
      continue;
    }
    // normal char
    if(mode !== "word"){ flush(); mode="word"; }
    buf += ch;
  }
  flush();
  return tokens;
}

function layoutTokensToCharPositions(ctx, text, maxWidth, letterSpacingPx, paddingLeft, paddingTop, lineHeightPx){
  const tokens = tokenizePreserve(text);
  const positions = []; // per char: {ch, x, y, indexInText}
  let x = paddingLeft;
  let y = paddingTop;

  const newLine = ()=>{
    x = paddingLeft;
    y += lineHeightPx;
  };

  let globalIndex = 0;

  for(const tok of tokens){
    if(tok === "\n"){
      // newline consumes one char in global index, but we don't draw it
      globalIndex += 1;
      newLine();
      continue;
    }

    const tokWidth = measureTokenWidth(ctx, tok, letterSpacingPx);

    if(x > paddingLeft && (x + tokWidth) > (paddingLeft + maxWidth)){
      // If token is spaces only: put them on next line too (like pre-wrap)
      // If token is a long word that doesn't fit even on empty line -> fallback to char wrap.
      const isSpaces = /^ +$/.test(tok);
      if(!isSpaces){
        // if it would fit on empty line, just wrap before it
        if(tokWidth <= maxWidth){
          newLine();
        }else{
          // hard case: single word longer than line => char wrap
          for(let i=0;i<tok.length;i++){
            const ch = tok[i];
const isSpace = ch === " ";
const glyph = isSpace ? NBSP : ch;

let adv = (ctx.measureText(glyph).width || 0);

// ✅ letter-spacing auch für Spaces anwenden
adv += letterSpacingPx;


            // if char doesn't fit, newline
            if(x > paddingLeft && (x + adv) > (paddingLeft + maxWidth)){
              newLine();
            }
            positions.push({ ch, x, y, indexInText: globalIndex });
            x += adv;
            globalIndex += 1;
          }
          continue;
        }
      }else{
        // spaces: wrap
        newLine();
      }
    }

for(let i=0;i<tok.length;i++){
  const ch = tok[i];
  const isSpace = ch === " ";
  const glyph = isSpace ? NBSP : ch;
  const gw = ctx.measureText(glyph).width || 0;

  // CSS-korrektes Advance
  const adv = gw + letterSpacingPx * (isSpace ? 2 : 1);

  if(x > paddingLeft && (x + adv) > (paddingLeft + maxWidth)){
    newLine();
  }

  // ⬅️ Space optisch zentrieren
  const drawX = x + (isSpace ? letterSpacingPx : 0);

  positions.push({
    ch,
    x: drawX,
    y,
    indexInText: globalIndex
  });

  x += adv;
  globalIndex += 1;
}

  }

  const usedLines = Math.max(1, Math.round(((y - paddingTop) / lineHeightPx) + 1));
  return { positions, usedLines };
}
function computeDynamicCanvasWidth(positions, ctx, letterSpacingPx, paddingRight){
  let maxX = 0;

  for(const p of positions){
    const ch = p.ch === " " ? NBSP : p.ch;
    const w = (ctx.measureText(ch).width || 0) + letterSpacingPx;
    maxX = Math.max(maxX, p.x + w);
  }

  return Math.ceil(maxX + paddingRight);
}

async function exportWebM(codec){
  if(!window.MediaRecorder){
    alert("MediaRecorder wird in deinem Browser nicht unterstützt.");
    return;
  }


const mimeMap = {
  vp8: "video/webm; codecs=vp8",
  vp9: "video/webm; codecs=vp9"
};

const mimeType = mimeMap[codec];


if(!MediaRecorder.isTypeSupported(mimeType)){
  alert(`Format ${codec.toUpperCase()} wird nicht unterstützt.`);
  hideExportOverlay();
  return;
}

  const FPS = 30;
  const HARD_CAP_MS = 16000; // +1s vs vorher (15000)
  const DONE_EXTRA_MS = 1000; // noch 1s "auslaufen", damit wirklich alles sichtbar scrambled ist

  showExportOverlay();


  const S = getExportStyleSnapshot();
  const text = input.value || "";

const canvas = document.createElement("canvas");

// temporär groß, um korrekt zu layouten
const TEMP_WIDTH = 4096;
canvas.width = TEMP_WIDTH;

const ctx = canvas.getContext("2d", {
  alpha: true,
  colorSpace: "srgb"
});

ctx.textBaseline = "top";

  // base layout font
  ctx.font = makeCanvasFont(S.fontFamilyUI, S.fontSizeBase, 300);
  const maxTextWidth = S.width - S.paddingLeft - S.paddingRight;

const { positions, usedLines } = layoutTokensToCharPositions(
  ctx,
  text,
  maxTextWidth,
  S.letterSpacingPx,
  S.paddingLeft,
  S.paddingTop,
  S.lineHeightPx
);



const dynamicWidth = computeDynamicCanvasWidth(
  positions,
  ctx,
  S.letterSpacingPx,
  S.paddingRight
);

canvas.width = Math.min(Math.max(dynamicWidth, 200), 1920);

// ✅ stabile, CSS-nahe Höhenberechnung
const maxY = positions.length
  ? Math.max(...positions.map(p => p.y))
  : S.paddingTop;

// letzte belegte Zeile + eine Zeilenhöhe (damit die Zeile “unten” Platz hat)
canvas.height = Math.ceil(
  (maxY + S.lineHeightPx) + S.paddingBottom
);


// 🔥 WICHTIG: NACH DEM LETZTEN RESIZE!
ctx.textBaseline = "top";
ctx.textAlign = "left";




  // Build per-char state aligned to positions (positions excludes '\n' chars)
  // Each char starts hidden until its startAtMs => NO cleartext frame
  const delayMsPerChar = 20; // sequential reveal
  const baseIterations = 6;
  const intervalForExtra = () => 1000 / Math.max(0.1, speed);

  // For speed-linking: each char advances scramble steps on schedule based on speed (like UI setTimeout)
const chars = positions.map((p, idx)=>{
  const extraIterations = maxExtraTime>0
    ? Math.floor(Math.random()*(maxExtraTime*1000/intervalForExtra()))
    : 0;

  return {
    ch: p.ch,
    x: p.x,
y: p.y,
    startAtMs: idx * delayMsPerChar,
    iter: 0,
    maxIter: baseIterations + extraIterations,
    locked: false,
    lastStepMs: null,
    lastDraw: null
  };
});


  const stream = canvas.captureStream(FPS);
const rec = new MediaRecorder(stream, {
  mimeType,
  videoBitsPerSecond: codec === "vp9" ? 3_000_000 : 6_000_000
});



  const chunks=[];
  rec.ondataavailable = (e)=>{ if(e.data && e.data.size) chunks.push(e.data); };
  rec.start();

  // Firefox warmup
  await new Promise(r=>setTimeout(r, 300));

  const startedAt = performance.now();
  let doneAt = null;

  function lockedProgress(){
    const total = chars.length || 1;
    let locked = 0;
    for(const c of chars) if(c.locked) locked++;
    return locked / total;
  }
  function allLocked(){
    for(const c of chars) if(!c.locked) return false;
    return true;
  }

  try{
    while(true){
      const now = performance.now();
      const t = now - startedAt;

      // background
      ctx.fillStyle = S.bg;
      ctx.fillRect(0,0,canvas.width,canvas.height);

      // draw each char
      for(const st of chars){
        // before start: draw nothing
        if(t < st.startAtMs) continue;

        // determine if we need to advance scramble steps based on speed (UI-like)
        const liveInterval = 1000 / Math.max(0.1, speed);

        if(!st.locked){
          if(st.lastStepMs == null){
            st.lastStepMs = t;
            // first visible draw should be scrambled (not cleartext)
            const pool = matrixMode ? matrixPool : normalPool;
            st.lastDraw = (st.ch===" " ? NBSP : pool[Math.random()*pool.length|0]);
          }else{
            // advance steps as many as required (in case FPS < speed)
            while((t - st.lastStepMs) >= liveInterval && !st.locked){
              st.lastStepMs += liveInterval;
              st.iter++;
              if(st.iter >= st.maxIter){
                st.locked = true;
                break;
              }else{
                const pool = matrixMode ? matrixPool : normalPool;
                st.lastDraw = (st.ch===" " ? NBSP : pool[Math.random()*pool.length|0]);
              }
            }
          }
        }

        // choose draw char + styles
        let drawChar, useColor, useFamily, useSize, yOffset;
        if(st.locked){
          drawChar = (st.ch===" " ? NBSP : st.ch);
          useColor = S.fg;
          useFamily = S.fontFamilyUI;
          useSize = S.fontSizeBase;
          yOffset = 0;
        }else{
          drawChar = st.lastDraw ?? (st.ch===" " ? NBSP : st.ch);
if(matrixMode){
  useColor = matrixGreen ? S.matrixGreen : S.fg;
}else if(randomColors){
  useColor = randColor();
}else{
  useColor = S.fg;
}


          useFamily = matrixMode ? matrixFont : rand(normalFonts);
          useSize = S.fontSizeBase * scrambleSize;

          yOffset = 0;
          if(superscript){
            useSize = useSize * 0.85;
            yOffset = -useSize * 0.28;
          }
        }

        ctx.fillStyle = useColor;
        ctx.font = makeCanvasFont(useFamily, useSize, 300);
        ctx.fillText(drawChar, st.x, st.y + yOffset);
      }

      const p = Math.round(lockedProgress()*100);
      exportPct.textContent = p + "%";
      exportBarInner.style.width = p + "%";

      const done = allLocked();
      const tooLong = (t >= HARD_CAP_MS);

      if(done && doneAt === null) doneAt = now;

      // Stop conditions:
      // - hard cap (now +1s vs previous)
      // - OR all locked + extra 1s
      const doneAndExtra = doneAt && (now - doneAt) >= DONE_EXTRA_MS;
      if(tooLong || doneAndExtra) break;

      await new Promise(r=>setTimeout(r, 1000/FPS));
    }
  } catch(err){
    console.error(err);
  }

  // Firefox flush
  await new Promise(r=>setTimeout(r, 300));
  try{ rec.requestData(); } catch(_){}
  await new Promise(r=>setTimeout(r, 50));

await new Promise(resolve => {
  rec.onstop = resolve;
  rec.stop();
});




  hideExportOverlay();

const outBlob = new Blob(chunks, { type: mimeType });

  const a=document.createElement("a");
  a.href = URL.createObjectURL(outBlob);
a.download = `scramble_${codec}.webm`;

  document.body.appendChild(a);
  a.click();
  a.remove();
  setTimeout(()=>URL.revokeObjectURL(a.href), 30000);
}

document.getElementById("exportVp8").onclick = () =>
  exportWebM("vp8");

document.getElementById("exportVp9").onclick = () =>
  exportWebM("vp9");


</script>

</body>
</html>
