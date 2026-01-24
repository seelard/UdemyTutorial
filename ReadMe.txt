
Install Node.js

	- https://nodejs.org/en/download/current
	- Browser-en kívül futtat javascript kódot
	- Angular-nak alapvetően nem lenne rá szüksége
		- de az Angular CLI (command line interface) használja 
		- illetve vele együtt telepszik az npm (node package manager), amely kell többek közt az Angular telepítéséhez is

Angular CLI (command line interface)

	Az Angular alap eszköze, parancssorból lehet létrehozni, publikálni, ... Angular alkalmazásokat.


Globally install Angular CLI v19

	Uninstall existing Angular

		npm uninstall -g @angular/cli


	(Optionally) clear npm cache:

		npm cache clean --force


	Install the desired global CLI version (v19):

		npm install -g @angular/cli@19


Use a per-project (local) CLI version / NPX

	Install Angular CLI v19 locally

		Use npx (telepíti a CLI-t egy új project létrehozásával egy új mappában, amiben létrehozza a package.json-t is)

			npx @angular/cli@19 new my-app

	Use npx to run the local CLI, eg:
		
		npx ng new my-app

	Or in existing project:

		npx ng update @angular/cli@19 @angular/core@19


Angular app létrehozása

	ng new first-angular-app
		- paraméterek megadása (y/N)...

	ng new first-angular-app --no-zoneless
		- v21+ esetén zoneless project az alapértelmezett, abban az esetben kell a --no-zoneless a példa alkalmazáshoz...

A generált Angular app elemei

	CLI verziótól függően lehetnek eltérések
		A létrehozáskor beállítható paraméterek is eltérhetnek
		File-ok elnevezése, elhelyezkedése, db száma is változhat
			- pl. újabb verziókban (Angular 20+) az app.component.ts helyett app.ts van

	tsconfig.json
		typescript specifikus beállítások

	package.json
		project specifikus konfigurációs adatok
		Projecthez tartozó modulok (+verziói) felsorolása
			- Dependencies
		...

	angular.json
		angular specifikus beállítások

	.editorconfig
	.gitignore

	/src
		/app
			app.component.css
			app.component.html
			app.component.ts
			...
		index.html, main.ts, styles.css

VS Code extensions (for Angular)

	Angular Language Service
		Fejlesztés közbeni Intellisense megoldások...

	Angular Essentials (John Papa)
	
	Auto Rename Tag
		HTML tag záró elemének automatikus állítása.

	Prettier

In a terminal

  Dependencies telepítés (pl. github-ról lehúzott project esetén)
		npm install

	Run a project	
		npm start

Components

	Korábban csak ún. module based components létezett, ahol a root mindenképpen egy module,
	amely tartalmazta a komponenseket

	Angular 16-tól létezik a standalone component
		@Component({ standalone: true })

	Angular 19-től az alapértelmezés a true, így a bejegyzés elmaradhat

	Standalone esetben nincs module a rendszerben,
	egy root komponensen (alap esetben az AppComponent) belül épül fel a rendszer akárhány komponensből (component tree).

Property binding

	HTML template-ben ts-ből jövő változók beemelése

	<img [src]="someSrc"> 
		- Ez a DOM element (HTMLImageElement) egy létező property-je (src).

	Lehet olyan eset, ahol egy HTML elem attribútumához kell bind-olni

	<td [attr.colspan]="columnSpan"></td>
		- A colspan egy attribútum (ilyen névvel nincs property), a bind-hoz kell az attr. előtag.

	https://jakearchibald.com/2024/attributes-vs-properties/

Change detection mechanisms

	zone.js
		Hagyományos megoldások, háttérben működő mechanizmusok minden UI-t érintő változás lekövetésére

	signals	
		signal-ok alkalmazása (nincs szükség a zone.js-re)
		Minden signal maga gondoskodik a hozzá tartozó UI aktualizálásáról

@Input()

	@Input({ required: true }) userId!: string;

	This means:
		“I just want the parent component to pass a value. I will access it normally inside the component.”

	This version has no setter, just a plain input property.	

	@Input() 
	set userId(uid: string) {
		console.log(uid);
	}

	This means:
		“I want to react to changes of the input, so I define a setter. Angular will call this setter whenever the value changes.”

	Here, @Input() decorates the setter, and there is no standalone property.

	If you want both a setter AND a getter, you must add a backing field:

		private _userId!: string;

		@Input({ required: true })
		set userId(uid: string) {
			this._userId = uid;
			console.log(uid);
		}

		get userId() {
			return this._userId;
		}

Signal inputs
	Lesson 33, 36

	input függvény
		@Input decorator helyett használható signal-okat használó project-ek esetén.
		Az input függvény eredménye egy readonly signal.

	output függvény
		@Output decorator helyett használható.
		Fontos különbség az input()-hoz képest, hogy ez nem eredményez signalt, ugyanúgy EventEmitter-t hoz létre.
		Ez inkább csak a szintaktikai egységesség kedvéért van, signalt használó projectek esetén...

Forms
	Egy Form-on belüli submit button viselkedése alapesetben kötött.
	Adatot küldene a server felé.
	Angular esetén a FormsModule importálásával ez a viselkedés felülíródik.
	Az ngSubmit alkalmazásával saját metódus hívható a submit esetén.

	Lesson 54	

input within a Form (Two-way binding)
	ngModel használatához az imput elem-nek lennie kel name attribútumának, ez célszerűen lehet ugyanaz, mint az id

Content Projection with ng-Content

	Lesson 57
	Egy adott komponens képes lehet beágyazni (wrap) különböző komponenseket is egy adott helyre.
	Ez lehet akár csak közös stílus alkalmazása miatt is. 
	Ahol a beágyazott elemek kapják a beágyazó elem (wrapper) stílusát.
	Ez van a példában is (a wrapper component csak ennyi):

	<app-card> komponens:

	<div>
		<ng-content>
	</div>

	Az alkalmazás helyén ezen belülre kell helyezni a beágyzandó komponenseket.

	<app-card>
		<article>
			...
		</article>
	</app-card>

Beépített alapelemek kiterjesztése (Extending Built in Elements)

	Ilyen pl. a <button>
	Nem szerencsés ilyenből custom komponenst csinálni.

	ButtonComponent with selector: 'app-button'

	<button>
		<span>
			Logout
		</span>
		<span class="icon">
			→
		</span>
	</button>

	A használat helyén 
	<app-button />

	Ebben az esetben a DOM-ban a generált HTML egy plusz beágyazást fog tartalmazni

	<app-button>
		<button>
			...
		</button>
	<app-button />

	Célszerűbb csak a button belső részéhez komponenst készíteni

		<span>
			Logout
		</span>
		<span class="icon">
			→
		</span>

	A felhasználáshoz a korábbi selector helyett más megoldás használható

	Type selector (ez a korábban is használt megoldás)
		selector: 'app-button'
	
		- ahol a komponens egy DOM element

	Attribute selector (ez a jelen esetben javasolt megoldás)
		selector: '[appButton]' - minden helyre beillesztendő, ahol az appButton attribútum szerepel
		kombinálható
		selector: 'button[appButton]' - minden button esetén beillesztendő, ahol az appButton attribútum szerepel

		<button appButton></button>

		az 'appButton' egy tetszőleges azonosító

		- Itt a button a DOM element és az attribútum mutatja a template komponenst, amivel bővítésre kerül

	Class selector
	 selector: '.menu-item' - minden helyre beillesztendő, ahol az adott css class meg van adva

	Selector-ok kombinálhatóak
	 selector: 'drop-zone, [dropZone]'

	:not pseudo class - bármely más selector-ral használható
		For example, you could define a [dropzone] attribute selector and prevent matching textarea elements:
		selector: '[dropzone]:not(textarea)' 

Component selectors
	https://angular.dev/guide/components/selectors

Content Projection
	Saját ButtonComponent változó tartalom megjelenés biztosítása input() (@Input) használata nélkül

  ng-content használata a button-component.html-ben

	<span>
		<ng-content/>
	</span>
	<ng-content select=".icon"/>

	Több ng-content használata önmagában nem működik, mert az Angular nem tudja eldönteni mit hová illesszen be.
	Szükséges az azonosítás. A select egy css selector, a fenti példában minden megfelelő, ami tartalmazza az 'icon' class-t.
	Így ott a <span> elemet a beágyazó helyre kell kirakni, hogy lehessen hozzá class-t megadni.

  <button AppButton>
    Submit
    <span class="icon">♦</span>
  </button>

	Az első ng-content nincs azonosítva, így az megkapja az összes olyan tartalmat, ami máshová nem illeszkedett.

	Ha nem szeretnénk a class megadást kívülre rakni:

	<span>
		<ng-content/>
	</span>
	<span class="icon">
		<ng-content select="icon"/>  --Itt ez egy item selector nem class selector (lekerült a .)
	</span>

  <button AppButton>
    Submit
    <span ngProjectAs="icon">♦</span>
  </button>

	Fallback value megadása az icon-hoz (ha nem lenne megfelelő elem a select-hez, akkor az jelenne meg)

	<span>
	  <ng-content/>
	</span>
	<ng-content select="icon">
		☺  
	</ng-content>

	Form elemben lévő label + valamilyen input elem kombináció általánosítása egy komponensben

	<p>
		<label>Title</label>
		<input name="title" id="title"/>
	</p>

	<p>
		<label>Request</label>
		<textarea name="request" id="request" rows="3"></textarea>
	</p>

	Ehhez egy komponens (AppControl):
		A select-nél felsorolás is állhat (ahogy korábban a selector esetén is), hogy milyen content elfogadott az adott helyre.

	<p class="control">
		<ng-content />
		<ng-content select="input, textarea" />
	</p>

	Beágyazása:

  <app-control>
    Title
    <input name="title" id="title"/>
  </app-control>

  <app-control>
    Request
    <textarea name="request" id="request" rows="3"></textarea>
  </app-control>

View encapsulation options to control CSS scoping

	- Van egy globális css - style.css, amely mindenhová kihatással van
	- Minden komponensnek saját css fájlja van, amely csak az adott komponensre érvényes
	- Ha egy komponensben ng-content által beépülő részek is vannak, a lokális css nem biztos, hogy tud működni,
		ugyanis a beépülő részek kiesnek a css hatóköréből.
		- Ezt adott esetben a @Component definícióban az encapsulation beállításával lehet módosítani
			- encapsulation: ViewEncapsulation.None - kikapcsolja a lokális láthatóságot (globálissá válik a css)

CSS :host slector

	- Hasonló eset, amikor egy alap elemhez készítünk komponenst 
		és az alap elemnek csak a belső része kerül a komponensbe (pl. button)

		Ekkor a komponens css-e nem tud érvényesülni.
		Erre egy megoldás a :host css kulcsszó használata

		Pl.:
		button:hover {
			background-color: #551b98;
		}
		helyett
		:host:hover {
			background-color: #551b98;
		}

		A :host a beágyazó elemet jelenti

	Megjegyzés:
		A :host selector az encapsulation: ViewEncapsulation.None beállítással együtt nem működik.
		Abban az esetben az adott css elveszti lokális jellegét, így nem értelmezhető hozzá a beágyazó elem.


@Component host property

	Ha magának egy komponensnek van szüksége class beállításra és azt szeretnénk,
	hogy ezt ne kelljen minden felhasználási helyen külön megadnia template(ek)ben.

	<app-control class="control" />

  @Component({
		selector: 'app-control',
		...
		encapsulation: ViewEncapsulation.None,
		host: {
			class: 'control'
		}
	})

	host property egy key-value párokat tartalmazó lista lehet, ebben az esetben a class megadással.

	Használható az encapsulation beállítása nélkül is.

	host property helyett használható a @HostBinding, ami egy régebbi használati módszer

	export class ControlComponent {
		@HostBinding() class = 'control'; // mivel a class foglalt név a ts-ben
		@HostBinding('class') className = 'control'; // ez a helyes ebben az esetben

		@HostListener('click') onClick() {
			console.log('clicked');
		} 

//		onClick() {
//			console.log('clicked');
//		}
	}

  Mivel a host property name-value párokat tartalmazhat így ott event listener is lehet.

			host: {
			class: 'control',
//			'(click)': 'onClick()'
		}

	Ugyanezt lehet megadni a @HostListener-rel, ami egy régebbi szintaxis,
	jelenleg a host property-s megadás a javasolt.

Programból való hozzáférés a host element-hez

	- Nem tipikus eset, mivel a HTML elemeket befolyásolni inkább egyéb Angular lehetőségekkel célszerű

	- Ha mégis szükséges, akkor a komponens class-ba kell injektálni egy speciális elemet.

	export class ControlComponent {
		private el = inject(ElementRef); // inject, ElementRef importálandó az '@angular/core'-beállítható

    // az el-en keresztül hozzáférhető a komponens host element-je (pl. <app-control>),
		// amelyen keresztül az összes tartalmazott elem.
		// Ez alacsony szintű hozzáférés (mint javascriptből), így használata nem célszerű...
	}

class binding in templates

	- Egy adott class binding
	
		<div [class.status]="currentStatus === 'online'">

	- Több class binding egyszerre

	<div [class]="{
		status: true,
		'status-online': currentStatus === 'online',
		'status-offline': currentStatus === 'offline',
		'status-unknown': currentStatus === 'unknown',
	}">

	Ahol a class név tartalmaz aláhúzás jelet, ott aposztrófok közé kell helyezni, mivel az nem megengedett jel a ts-ben.

style binding in templates

	- Hasonlóan a class binding-hoz, lehet egy adott style-ra

	[style.fontSize]="'64px'"  // Mivel bind-ról van szó, valid ts elemnek kell szerepelni, string konstans esetén kell az aposztróf!

	Erre egy példa van a traffic.component.htm-ben

	- Több style binding egyszerre

	[style]="{
		'font-size': '64px', // itt használható camelCase, aposztrófok nélkül: fontSize 
		...
	}"

Component Lifecycle

	Egy komponens objektum létrejöttekor a konstruktora meghívódik, ami egy standard javascript/typescript viselkedés.
	Vannak egyéb Angular függvények, amelyek adott időpontokban hívódnak.

	ngOnChanges
		A komponens valamely input property-je változik.
		- Egy hashtáblában az érintett property(k) neve(i), mint key és a hozzájuk tartozó változást leíró objektum (SimpleChange)

	ngOnInit
		Konstruktor helyett ide ajánlott helyezni a legtöbb komponens inicializáló kódot.
		Pl. egy input property inicializálása sem történik meg még a konstruktorban, innen viszont már elérhető.
	
	ngDoCheck
		Angular change detection mechanizmusához kötődik. Meghívódik, ha bármi a UI-on aktualizálást igényel
		(bárhol az alkalmazásban, nem csak az adott komponensben).
		Így ez elég gyakran hívódik, ennek használata ritkán lehet szükséges.

	ngAfterViewInit
		A view jelenti a komponens template-ben lévő elemeket
		Akkor hívódik, amikor minden template-ben lévő tartalom már inicializálásra került.
		A @ViewChild és tsi által behúzott HTML elemek onnantól elérhetőak, amikor ez meghívódik,
		tehát pl. az ngOnInit-ből még nem lesznek azok.

	ngAfterViewChecked
		Akkor hívódik, amikor minden template-ben lévő tartalmat már ellenőrzött az Angular change detection mechanizmusa.

	ngAfterContentInit
		A content jelenti a kívülről projektált elemeket, amelyek nincsenek közvetlenül a template-ben (ng-content).
		Akkor hívódik, amikor minden projektált tartalom már inicializálásra került.
		A @ContentChild és tsi által behúzott HTML elemek onnantól elérhetőek, amikor ez meghívódik,
		tehát pl. az ngOnInit-ből még nem lesznek azok (valószínű még az ngAfterViewInit-ből sem).

	ngAfterContentChecked
		Akkor hívódik, amikor minden projektált tartalmat már ellenőrzött az Angular change detection mechanizmusa.

	ngOnDestroy
		Az adott komponens láthatóságának kikapcsolásakor, amikor kikerül a renderelendő komponensek közül.
		Pl. feltételes renderelés van a template-ben @if

	afterRender (afterEveryRender >=v20), afterNextRender
		Angular 16-tól elérhetőek, v20-tól afterRender -> afterEveryRender-re átnevezve

		Nem a többi life cycle hook metóduson keresztül érhetőek el (ngOnInit,...), hanem a konstruktorban.

		constructor() {
			// A regisztrált callback minden UI változáskor meghívódik (amikor újra kell renderelni valamit)
			afterRender(() => {
				console.log('EVERY');
			});

			// A regisztrált callback a következő UI változáskor meghívódik egyszer (amikor újra kell renderelni valamit)
			// Jól használható olyan inicializálásokra, ami a komponens megjelenése után kell csak egyszer.
			afterNextRender(() => {
				console.log('NEXT');
			});
		}

		NEM CSAK AZ ADOTT KOMPONENSRE ÉRTENDŐ, HANEM AZ EGÉSZ WEB SITE-RA (ANGULAR APPLICATION-re)!

DestroyRef használata ngOnDestroy helyett

	Egy újabb lehetőség, ami régebbi verziókban nincs (>=v16)

	private destroyRef = inject(DestroyRef);

	// Az OnItit-ben, a setInterval után egyből regisztrálható a megszüntető kód...
	this.destroyRef.onDestroy(() => clearInterval(intervalId));

	A komponens kódjában több helyen is használható (több helyről is fel lehet iratkozni az onDestroy-ra).

	Talán ennyivel elegánsabb, mint az ngOnDestroy, ahol egy összevont helyen van minden megszüntető kód.
	Itt pedig egymás mellett szerepelhet a létrehozás-megszüntetés ???

ReturnType<>

	SetInterval visszatérési értéke NodeJS.Timeout, ezzel problémája van az Angularnak
  
	Ez működik ebben az esetben:

	  private intervalId?: ReturnType<typeof setInterval>;

		intervalId = setInterval(...)

Template variables

	<form (ngSubmit)="onSubmit(titleInput)">
		<app-control>
			Title
			<input name="title" id="title" #titleInput/>
		</app-control>
		...

	#titleInput a template variable, amelynek #-el kell kezdődnie
	A felhasználás helyén már nem kell a #: "onSubmit(titleInput)"

	Itt egy konkrét HTMLElement kerül átadásra.

	https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement

	Jelen esetben HTMLInputElement, amelynek "value" property-je tartalmazza a beírt szöveget:

	onSubmit(titleElement: HTMLInputElement) {
    console.log(titleElement.value);
  }

	Az átadott paraméter a template-ben lehet egyből a value
		"onSubmit(titleInput.value)"
	Ekkor az egy string
		onSubmit(title: string) {
			console.log(title);
		}

	Bármely template elemhez adható template variable.

	<button AppButton #btn>
		Logout
		<span class="icon" ngProjectAs="icon">♠</span>
	</button>

	Ebben az esetben a btn egy ButtonComponent típusú lesz (saját button komponensre van állítva az AppButton attribútummal).

	Az AppButton megadás nélkül
	<button #btn>
	simán HTMLButtonElement lenne a típusa

@ViewChild, @ViewChildren

	Hozzáférést biztosít a template elemeihez a ts kódban.
	Az ngAfterViewInit meghívódásakor lesznek elérhetőek az így lekérdezett elemek, előtte még nem.

	@ViewChild(selector) htmlItem

	selector
		- class name (ts osztály név (többnyire egy saját komponens), pl. ButtonComponent)
			Ha több ilyen elem is van, akkor az elsőt adja vissza.
		
		- string, ami lehet egy template variable név, pl. 'form'
			ahol a template pl. <form (ngSubmit)="onSubmit()" #form>...

	@ViewChild('form') myForm?: ElementRef<HTMLFormElement>;
		- Kell a ?, mert nem azonnal jön létre, pl. a konstruktorból még nem lenne elérhető
			(illetve előfordulhat, hogy nincs a selector-nak megfelelő elem)

	  onSubmit() {
 	   this.myForm?.nativeElement.reset();  // Szintén kell a ?, 
		 																			// illetve a nativeElement (wrapper-ben így elérhető a konkrét elem)
																					// reset a HTMLFormElement metódusa
  	}

	@ViewChildren
		- Ha több elem is megfelelhet a selector-nak és szükség is van rájuk
		
		@ViewChildren('myDiv') myDivs!: QueryList<ElementRef<HTMLDivElement>>;

	ElementRef
		Wrapper egy natív DOM elemhez
		Egy biztonságosabb hozzáférést biztosít a valódi HTML elemekhez.

	QueryList
		Egy élő gyűjtemény (ilyet ad vissza a @ViewChildren és @ContentChildren), amely lekérdezés után is változhat,
		pl. egy @if vagy @for (*ngIf, *ngFor) hatására módosul a megjelenített elemek száma.


	viewChild(), viewChildren() függvények (signal)
		Újabb lehetőség a @ViewChild, @ViewChildren helyett

		viewChild(selector)

		private myForm = viewChild<ElementRef<HTMLFormElement>>('form');

	 	this.myForm()?.nativeElement.reset(); // Kell a ?

		// required alkalmazásával
		private myForm = viewChild.required<ElementRef<HTMLFormElement>>('form');

	 	this.myForm().nativeElement.reset(); // Nem kell a ?

@ContentChild, @ContentChildren

	A @ViewChild-hoz hasonlóan hozzáférést biztosít a template elemeihez a ts kódban.
	Lehetnek a template-be projektált részek (ng-content), amelyek tartalma nem részei a template-nek.
	Ezek esetében a @ContentChild használható

	Az ngAfterContentInit meghívódásakor lesznek elérhetőek az így lekérdezett elemek, előtte még nem.

	Ugyanúgy van contentChild() és contentChildren() függvény (signal) az újabb verziókban.

	Az egyes elemek itt is azonosíthatóak többek közt template variable megadással.
	Itt a template variable-nek a beágyazó résznél kell szerepelnie

  <app-control>
    Title
    <input name="title" id="title" #myInput/>
  </app-control>

  <app-control>
    Request
    <textarea name="request" id="request" rows="3" #myInput></textarea>
  </app-control>

	Ebben az esetben ugyanaz a template variable több különböző helyen is szerepel!!!
	Magában a komponensben mindig csak az egyik, mivel mindegyik egy külön komponens.

  Komponens template (app-control):

  <ng-content />
  <ng-content select="input, textarea" />

	A ControlComponent-ben a 'myInput' template variable használható a @ContentChild paramétereként.
	A kapott elem típusa HTMLInputElement | HTMLTextAreaElement.

effect()
	LifeCycle related függvény, de inkább signal-okhoz tartozik.

	Egy komponensben alkalmazott signal esetén a template-ben lévő (bind-olt) elemek automatikusan követik
	a signal változásait (automatikusan feiratkoznak rá).

	Arra használható az effect(), ha a ts kódban szeretnénk értesülni a változásról.

	effect(() => {
		console.log(this.mySignal());
	})

	Az effect paraméterében kap egy callback függvényt, az abban szereplő signal-ok bármelyikének változása esetén meghívódik.

	const count = signal(0);
	const name = signal('Alice');

	// Effect 1 — depends on count
	effect(() => {
		console.log('Count changed:', count());
	});

	// Effect 2 — depends on name
	effect(() => {
		console.log('Name changed:', name());
	});

	// Effect 3 — depends on both
	effect(() => {
		console.log(`Combined: ${name()} - ${count()}`);
	});

	CleanUp logic in effect()

	effect((onCleanup) => {
		const tasks = getTasks();
		const timer = setTimeout(() => {
			console.log(`Current number of tasks: ${tasks().length}`);
		}, 1000);
		onCleanup(() => {
			clearTimeout(timer);
		});
	});

	- a getTasks() egy signal-t ad vissza (ez lehet a figyelt signal, amely változására hívódik ez a callback)
	- onCleanup() regisztrál egy másik callback függvényt (ez a külső callback paraméterében meg is marad)
		Ez lehetőséget biztosít a következő változáskor, hogy először ez a függvény fusson le.
	- Az onCleanup meghívásra kerül akkor is, ha az effect megszűnik (destroyed), ami pl. a komponens megszünésekor történik.

@for fallback ág -> @empty

	Ha nincs eleme a @for-ban lévő listának (tömbnek)

	@for (ticket of tickets; track ticket.id) {
		<li>
			<app-ticket/>
		</li>
	} @empty {
		<p>No tickets available</p>
	}

@for helper variables

	$first (true/false)
	$last (true/false)
	$odd (true/false)
	$even (true/false)
	$count (elemszám)
	$index (aktuális elem indexe)


Custom two-way binding (ngModel használata nélkül)
	(v17-től már használható a model() függvény)

	app.component.ts
		Itt van a mindenfelé bind-olt rectSize property, ami egy sima property (lehetne signal is, de nem az)

		export class AppComponent {
			rectSize = {
				width: '100',
				height: '100',
			};
		}

	app.component.html
		A komponensen belüli input mezőkhöz ngModel-es two-way binding van (ez a megoldás, ami form/input elemre működik)
		
		Egyéb esetben is szükség lehet two-way binding-ra (oda-vissza működjön az értékek automatikus közvetítése)
		A rect komponens felé van a custom two-way binding, itt is a kettős zárójel használatos [(size)]
			- Persze ehhez a .ts-ben is szükséges majd a megfelelő kód...

		<div id="inputs">
			<p>
				<label>Width</label>
				<input type="number" step="1.0" [(ngModel)]="rectSize.width" />
			</p>
			<p>
				<label>Height</label>
				<input type="number" step="1.0" [(ngModel)]="rectSize.height" />
			</p>
		</div>

		<app-rect [(size)]="rectSize"/>

		model() alkalmazása esetén
			[(size)]="rectSize", a size egy signal a rectSize sima property, de az Angular ezt kezeli

	rect.component.html
		Simán használva van az @Input-tal érkező size

		<div
			id="rect"
			[style.width]="size.width + 'px'"
			[style.height]="size.height +'px'"
			(click)="onReset()"
		></div>

		model() alkalmazása esetén
			[style.width]="size().width + 'px'"
			[style.height]="size().height +'px'"
			- kellenek a zárójelek (size()), mivel size itt signal

	rect.component.ts
		A two-way binding-hoz kell egy @Input és egy @Output a két irányhoz.
		Az @Input a szokásos, itt érkezik az érték.
		Az @Output esetén az ELNEVEZÉS KÖTÖTT, size -> sizeChange (Change utótag)

		@Input({ required: true }) size!: { width: string; height: string };
		@Output() sizeChange = new EventEmitter<{ width: string; height: string }>();

		onReset() {
			this.sizeChange.emit({
				width: '200',
				height: '100'
			});
		}

		model() alkalmazása esetén
		  size = model.required<{ width: string; height: string }>();

			- @Input/@Output helyett model() függvény, ami egy signal-t ad.

			onReset() {
				this.size.set({
					width: '200',
					height: '100'
				});
			}

			- emit helyett size signal beállítása (set)

Directives

	Olyan Angular elemek, amelyek valamilyen plusz működést adnak a felhasználás helyén az adott elemhez.

	Pl. ngModel, egy input HTML elemben használható (többek közt) two-way binding-hoz.
	Ehhez természetesen sok háttérműködés kell, hogy csinálja a dolgát.
	A direktíva használatával az Angular biztosítja a szolgáltatást.

	Egy direktíva is egy Angular kód, hasonlóan egy komponens vagy service-hez.

	@Directive{
		selector:...
		providers:...
		exportAs:...
	}
	export class NgModel extends... {
		...
	}

	Attribute Directives
		Pl. az ngModel egy attribute directive, mivel egy meglévő HTML elemen belül van, mint attribútum.

	Structural Directives
		Pl. *ngIf, *ngFor
		Ez szintén egy HTML elemen belül van, de a * megkülönbözteti
		Míg az attribute directives plusz működéseket eredményeznek (pl az input elem esetén), de nem változtatják meg 
		DOM-on belüli elhelyezkedést, addig a structural directivák a DOM-beli struktúrát befolyásolják 

		Az újabb Angular verziókban már a @if és @for használatos, amelyek nem direktívák.
		De a régebben készült Angular projektekben még ezek vannak...

Custom Attribute Directive létrehozása

	ng generate directive SafeLink

	vagy kézzel is létre lehet hozni: safe-link-directive.ts

	import { Directive } from "@angular/core";

	@Directive({
		selector: 'a[appSafeLink]',
		standalone: true,
		host: {
			'(click)': 'onConfirmLeavePage($event)'
		}
	})
		
	export class SafeLinkDirective {

		constructor() {
			console.log('SafeLinkDirective is active!');
		}
		
		onConfirmLeavePage(event: MouseEvent) {
			const sure = window.confirm('Are you sure?');

			if (sure) {
				return;
			}

			event?.preventDefault();
		}
	}

	A példa anchor elem viselkedésének kiterjesztése.

	Amikor kattintanak rá, feldob egy kérdést, hogy tuti el akar-e navigálni.
	Ha nem, akkor preventDefault() hívás, ami így nem hajtja végre az átnavigálást.

	selector:
		Úgy használatos, mint a @Component-nél,
		itt attribute selector van (a[appSafeLink]), kiegészítve az a-val, ami css szerűen jelzi,
		hogy <a> elemek esetén, amelyekre alkalmazva van az appSagfeLink attribútum.

	<ul>
		<li>
			<a href="https://angular.dev" appSafeLink>Angular Documentation</a>
		</li>
		<li>
			<a href="https://academind.com/courses" appSafeLink>Academind Courses</a>
		</li>
		<li>
			<a href="https://www.google.com/search?q=angular" appSafeLink>Google</a>
		</li>
	</ul>

	host: property, ahogy a @Component-nél itt lehet név-érték párokkal kiegészíteni a template-ben lévőket.
		Ez lehet event is (click). Így minden ilyen anchor-hoz hozzáíródik ezen event kezelése...

		- Itt használható a @HostListener, ahogy a @Component-nél.

Imput átadása Custom Directive-nek

  <a href="https://angular.dev" appSafeLink="myapp-docs-link">Angular Documentation</a>

	- "myapp-docs-link" az átadandó input

	A komponensben kell egy @Input vagy input()

	  queryParam = input('myapp'); // default a 'myapp' ha nem lenne átadott input

	A név queryParam, így azt egyeztetni kell az átadáshoz:

  	<a href="https://angular.dev" appSafeLink queryParam="myapp-docs-link">Angular Documentation</a>
			- nem kell a szögletes zárójel [queryParam], mivel nem egy Angular property van bind-olva, hanem egy konstans érték.

		Ha nem szeretnénk nevet megadni a template-ben
			- property neve ugyanaz legyen, mint az attribútum neve: appSafeLink (queryParam helyett)
			- alias használható
			  queryParam = input('myapp', { alias: 'appSafeLink'});
			- Akkor mehet a eredeti fenti megadási forma
			  <a href="https://angular.dev" appSafeLink="myapp-docs-link">Angular Documentation</a>


		onConfirmLeavePage(event: MouseEvent) {
			const sure = window.confirm('Are you sure?');

			if (sure) {
				(event.target as HTMLAnchorElement).href += '?from=' + this.queryParam();

				return;
			}

			event?.preventDefault();
		}

Dependency Injection in Directives

	Ugyanúgy alkalmazható, ahogy komponensek esetén, pl. a host element beadása a direktívába:

		private hostElementRef = inject<ElementRef<HTMLAnchorElement>>(ElementRef);

		- Itt van extra információ is arról milyen helyen használható a direktíva, így azzal kiegészíthető
			<ElementRef<HTMLAnchorElement>>

		onConfirmLeavePage(event: MouseEvent) {
			const sure = window.confirm('Are you sure?');

			if (sure) {
	      this.hostElementRef.nativeElement.href += '?from=' + this.queryParam();

				return;
			}

			event?.preventDefault();
		}

Custom Structural Directive létrehozása

	Pl. egy új direktíva (auth):
		ng g d auth/auth

	app-component-html-ben:
  	Az @if helyett valahogy így szeretnénk használni,
		a paragrafus az átadott érték szerint jelenik meg vagy sem.

		<p *appAuth="admin">Only admins should see this!</p>

		app.component.ts-ben az imports-hoz kell adni az AuthDirective osztályt!

		A * nélküli esetben eddig ez egy input átadás, ahogy az attribute direktívánál:

		<p appAuth="admin">Only admins should see this!</p>

	auth.directive.ts:

		export class AuthDirective {
			private authService = inject(AuthService);
			private templateRef = inject(TemplateRef);
			private viewContainerRef = inject(ViewContainerRef);
			userType = input.required<Permission>({ alias: 'appAuth'});

			constructor() {

				// Két signal is van, ha valamelyik változik, ez a kód lefut...
				effect(() => {
					if (this.authService.activePermission() === this.userType()) {
						console.log('SHOW');
					}
					else {
						console.log('NOT SHOW');
					}
				});
			}
		}

	Most már csak az kell, hogy a megfelelő hatás a DOM elemeken is látszódjon,
	amitől Structural lesz a Directive.

		<ng-template appAuth="admin">
			<p>Only admins should see this!</p>
		</ng-template>

		Az <ng-template> egy speciális Angular elem, az átala tartalmazott rész alapból nem jelenik meg.
		Így lehetőség van annak megjelenítésének vezérlésére.

	Két újabb elemet kell injektálni:
			private templateRef = inject(TemplateRef);
			private viewContainerRef = inject(ViewContainerRef);

		- TemplateRef: az ng-template által tartalmazott elem(ek)
		- ViewContainerRef: A hely ahol a template elemek megjelennek (vagy nem)

		Ezek használhatóak a DOM megjelenítés vezérlésére:

    effect(() => {
      if (this.authService.activePermission() === this.userType()) {

	// createEmbeddedView: Angular function létrehozni egy DOM elemet egy megadott helyen...
        this.viewContainerRef.createEmbeddedView(this.templateRef);
      }
      else {
         this.viewContainerRef.clear();
      }
    });

		A * előtag egy szintaktikai megoldás, aminek hatására az <ng-template> beágyazás automatikusan megtörténik.
		(*ngIf, *ngFor is ilyen)

    <p *appAuth="'admin'">Only admins should see this!</p>

		- Annyi változik, hogy itt automatikusan property binding-ot vár (mintha [] közt lenne),
			ezért a string konstantst aposztrófok közé kell rakni.

Host Directives & Composition

	Előfordul olyan eset, hogy egy adott direktíva funkcionalitására több különböző komponensnek vagy másik 
	direktívának is szüksége van (fixen használja azt)

	Erre használható a hostDirectives property:

	Direktíva használ másik direktívát:

	@Directive({
		selector: 'a[appSafeLink]',
		standalone: true,
		host: {
			'(click)': 'onConfirmLeavePage($event)'
		},
		hostDirectives: [LogDirective]
	})

	Komponens használ másik direktívát:

	@Component({
		selector: 'app-auth',
		standalone: true,
		imports: [FormsModule],
		templateUrl: './auth.component.html',
		styleUrl: './auth.component.css',
		hostDirectives: [LogDirective]
	})

	Ebben az esetben a selector atribútum nem jelenik meg a template-ben, hanem fixen beépül.

Pipes

	Módszer a megjelenített adatok átalakítására (transform)

	<td>{{ result.total | currency }}</td>

	currency egy beépített pipe a pénznemekre.

	Van néhány egyéb beépített pipe.

	<td>{{ CurrentDate | date:'medium' }}</td>

	Kettősponttal elválasztva adhatók át paraméterek.

  <p>New York: {{ currentTemperaturs.newYork | number: '1.1-2'}}</p>

	: '1.1-2'
		- Minimum számjegyek száma a tizedespont előtt
		- Minimum számjegyek száma a tizedespont után
		- Maximum számjegyek száma a tizedespont után

Custom pipe létrehozása

	ng generate pipe MyPipe

	pl. temperature.pipe.ts

	import { Pipe, PipeTransform } from "@angular/core";

	@Pipe({
		name: 'temp', // Az a név, ami a template-ben szerepelhet
		standalone: true,
	})

	// A Pipe osztálynak meg kell valósítania a PipeTransform interface-t
	// Ez megköveteli a transform függvényt.
	export class TemperaturePipe implements PipeTransform {

		transform(value: any, ...args: any[]) {

			return value + ' - transformed';
		}
	}

	- paraméterek típusa szükség szerint szűkíthető
	- args tetszőleges számú paraméter lehet (lehet, hogy nincs egy sem)

    	transform(value: string | number, inputType: string, outputType?: string) {
				- az inpuType kötelező (legalább egynek lenni kell a template-ben, különben kiabál a fordító)
				- az outputType opcionális

	  transform(value: string | number) {
			let val: number;

			if (typeof value === 'string') {
				val = parseFloat(value);
			}
			else {
				val = value;  
			}

			const outputTemp = val * (9 / 5) + 32;

			return `${outputTemp} °F`
		}

		<p>New York: {{ currentTemperaturs.newYork | temp: 'cel' : 'fah'}}</p>

Pipe-ok láncolása

	Tetszőlegesen egymás után helyezhetőek a pipe-ok, de a kimeneti és a bemeneti típusokat egyeztetni kell.

	Pl. a fenti hőmérséklet kijelzés tizedes jegy beállítására kézenfekvő lenne a DecimalPipe

		<p>New York: {{ currentTemperaturs.newYork | number: '1.1-2' | temp: 'cel' : 'fah'}}</p>

	Az egyik probléma, hogy a decimalPipe-nak number | null kimenete van.
	Ez orvosolható a temp pipe típus kiegészítésével (null-t is fogadjon)

	Mégsem hozza a megfelelő eredményt, mivel a hőmérséklet átszámítás a tizedesjegy beállítás 
	után van, ahol az eredmény nem garantálja a tizedesjegyek számát.

	Itt célszerűbb a temp pipe-ban megoldani ezt, pl.: toFixed(2)

Tömb rendezett megjelenítése pipe-pal

	// A value típusát a kívánt típustra kell állítani.
	transform(value: string[] | number[], direction: 'asc' | 'desc' = 'asc'): (string | number)[] {

		let sorted = [...value]; // ez egy (string | number)[] típust ad vissza, a return type ezt kapta.
		
		if (direction === 'asc') {
			sorted.sort((a, b) => {
				return a > b ? 1 : -1;
			});
		}
		else {
			sorted.sort((a, b) => {
				return b > a ? 1 : -1;
			});
		}

		return sorted;
	}

	<ul>
		@for (temperature of historicTemperatures | sort : 'desc'; track temperature) {
			<li (click)="onReset($index)">{{ temperature }}</li>
		}
	</ul>

	Pipe lehetséges káros mellékhatásai

		Ha az eredeti tömb valamelyik eleme változik, nem kerül frissítésre a kijelzés.
		Ha nem lenne pipe, akkor működne, akkor reagál rá a change detection.
		Pipe esetében viszont azért, hogy ne renderelődjön túl sokszor újra a kijelzés egy cache-ből dolgozik.
		A pipe csak akkor aktiválódik, ha maga a tömb referencia módosul, ha csak egy eleme, akkor nem.

		Erre lehet egy megoldás ha minden esetben új tömböt hozunk létre
		vagy a pure property:

			@Pipe({
				name: 'sort',
				standalone: true,
				pure: false // default true
			})

			Ekkor minden lehetséges esetben lefut a pipe.

		A pipe-pal rendezett kijelzés másik hátránya, hogy az index-ek elcsúsznak az eredeti és a kijelzett tömbb esetén.
		Ez hibaforrás lehet.
		Megoldásként:
			- index helyett egyéb azonosító használata
			- helyben rendezett tömb megjelenítése pipe helyett

Services Deep Dive

	export class TasksService{ 
		private tasks = signal<Task[]>([]);
		allTasks = this.tasks.asReadonly();
	
		addTask(taskData: { title: string;  description: string }) {
			const newTask: Task = {
				...taskData,
				id: Math.random().toString(),
				status: 'OPEN'
			}
			this.tasks.update((oldTasks) => [...oldTasks, newTask]);
		}

		updateTaskStatus(taskId: string, newStatus: TaskStatus) {
			this.tasks.update((oldTask) => oldTask.map((task) =>
				task.id === taskId ? { ...task, status: newStatus } : task));
		}
	}

	tasks - szolgáltatott adatok tároló tömbje
	
	allTasks - asReadonly(): egy writable signal readonly verzióját adja vissza
		Biztosítja, hogy a service-en kívülről ne lehessen közvetlenül módosítani

	addTask - új task hozzáadása
		Célszerű új tömb létzrehozása az Angular change detection segítésére.
	
	updateTaskStatus - szintén új tömböt hoz létre, a módosított elem új objektum lesz.

Service registration

	Valahogy tudatni kell az Angularral, hogy mely részek az injektálhatóak.
	Több típusú háttér elem is van (injector osztály), amely ilyen feladatokat végez.

	Application root / EnvironmentInjector

		A legáltalánosabb megoldás a service definícióban használt:

			@Injectable( {
				providedIn: 'root'
			})

		Másik lehetőség a main.ts-ben (ebben az esetben nincs @Injectable direktíva alkalmazva):

			bootstrapApplication(AppComponent, {
				providers: [TasksService]
			}).catch((err) => console.error(err));

			Hozzáadott rész: { providers: [TasksService] }

		Különbség a kettő között, hogy a bootstrap-es esetben a service kód teljes egészében hozzáadásra kerül,
		míg a másik esetben lehetősége van az Angular-nak optimalizálásra (csak a szükséges kódok használatára).

	ElementInjector

		Az @Injectable helyett egy olyan komponensben, amely tartalmazza azokat a komponenseket, 
		amelyeknek szükségük van az adott service-re:

		@Component({
			selector: 'app-tasks',
			standalone: true,
			templateUrl: './tasks.component.html',
			imports: [NewTaskComponent, TasksListComponent],
			providers: [TasksService],
		})

		providers property megadása.
		Csak ebben a komponensben és az általa tartalmazott (child) komponensekben lesz elérhető a service,
		pl. az AppComponent-ben nem.

		Másik fontos dolog az ElementInjector-ral, hogy ha az adott komponens több példányban szerepel az 
		applikációban, akkor mindegyiknek külön service-e lesz (két külön service példány).

Service injektálása service-be

	- Application root / EnvironmentInjector-nál leírt két módszer használható
	- Az ElementInjector nem működik ebben az esetben

Angular DevTools a browser-ben tartalmaz egy Injector Tree eszközt...

Custom DI Tokens (Dependency Injection Tokens)

	Ha a main.ts-beli service regisztrációt nézzük:

	bootstrapApplication(AppComponent, {
		providers: [TasksService]
	}).catch((err) => console.error(err));

	A providers ilyen megadása egy szintaktikai rövidítése annak, ami valójában van:
	Angular készít egy Provider objektumot a TasksService helyén (a providers property típusa ezt várja).
	Ez tartalmaz egy injection token-t és így azonosít egy injektálható elemet.

	A rövidített megadásban a TasksService megadás lesz a token.
	A felhasználás helyén az inject paramétere is egy ProviderToken típus, de ott is a TasksService van megadva.

	Ilyen tokent magunk is létrehozhatunk.

	main.ts-ben
	
	// export kell, mert szükség lesz rá kívül is
	// A paraméter description string tetszőleges.
	export const TaskServiceToken = new InjectionToken<TaskService>('tasks-service-token');

	bootstrapApplication(AppComponent, {
		providers: [{provide: TasksServiceToken, useClass: TasksService}]
	}).catch((err) => console.error(err));

	A providers megadásnál a rövidített megadás helyett kifejtjük az objektumot
		- provide: saját token
		- useClass: A service osztály (a useClass helyett lehet más is, de most egy osztályról van szó (ami a service))

	A felhasználás helyén:

	- inject
		  private tasksService = inject(TasksServiceToken);

	- in constructor
		  // Szükséges az @Inject decorator
			constructor( @Inject(TasksServiceToken) private tasksService: TasksService) {}

	Ebben az esetben ez csak egy demonstráció, hogy hogyan lehet saját tokent létrehozni,
	mivel itt a rövidített megadást célszerű használni (de a háttérben ekkor is ez van).

	Lehetséges azonban nem class jellegű elemeket is injektálhatóvá tenni, amihez saját token kell.

	Pl.:

		Egy konstans tömb, amely az applikáció több részén is használható.
		Tegyük ezt injektálhatóvá.

		task.model.ts

		// Típus definíció a jobb átláthatóságért.
		export type TaskStatusOptions = {
			value: 'open' | 'in-progress' | 'done';
			taskStatus: TaskStatus;
			text: string;
		}

		// Injection token
		export const TASK_STATUS_OPTIONS = new InjectionToken<TaskStatusOptions[]>('task-status-options');

		// A tömb amit injektálhatóvá teszünk
		export const TaskStatusOptions: TaskStatusOptions[] = [
			{
				value: 'open',
				taskStatus: 'OPEN',
				text: 'Open'
			},
			{
				value: 'in-progress',
				taskStatus: 'IN_PROGRESS',
				text: 'In-progress'
			},
			{
				value: 'done',
				taskStatus: 'DONE',
				text: 'Completed'
			},
		];

		// Provider definíció, ami a felhasználás helyén kell (ez lehetne ott is, de így rendezettebb)
		export const taskStatusOptionsProvider: Provider = {
			provide: TASK_STATUS_OPTIONS,
			useValue: TaskStatusOptions 
		};

		useValue van a useClass helyett mivel itt nem egy osztályról van szó, hanem egy konkrét tömbről.
		És itt a TaskStatusOptions a változót jelöli nem pedig a típust (sajátosan ugyanaz a nevük)...

		tasks-list.component.ts

		- Elég ide regisztrálni az injektálható elemet, mivel itt és egy child-ban lesz felhasználva.

		@Component({
			selector: 'app-tasks-list',
			standalone: true,
			templateUrl: './tasks-list.component.html',
			styleUrl: './tasks-list.component.css',
			imports: [TaskItemComponent],
			providers: [taskStatusOptionsProvider] // Kívül definiált provider, elérhető lesz itt és child komponensekben
		})

	  taskStatusOptions = inject<TaskStatusOptions[]>(TASK_STATUS_OPTIONS);

		Ezzel használható a template-ben a tömb:

    @for (option of taskStatusOptions; track option.value) {
      <option [value]="option.value">{{ option.text }}</option>
    }

		A child komponensben már csak inject kell, ott nem kell külön regisztrálni
		és ott is elérhető lesz a template-ben...

Change Detection mechanisms

	zone.js
		- Minden template binding-ban szereplő elemet, event lister-eket az egész applikációban figyeli
			hogy szükséges-e új érték beállítása a DOM számára

		- A példa alkalmazásban getter-ek vannak bind-olva (interpolation) {{ debugOutput }} minden komponensben külön.
			Bárhol változás történik a getter-ekben lévő log mindenhol jelez, hogy belefutott a végrehajtás
			független attól melyik komponensben van (ezt az Angular végzi, amikor is ellenőrzi volt-e változás).

		- Ez jó sokszor megtörténik a háttérben...

		- Éppen ezért template-be bindolt getter-ekben nem célszerű komplex műveleteket végezni.

		- A pipe-oknál említett (by default) cache megoldás is ezért van (ne fusson le indokolatlanul sokszor)

		Change Detection during Development

			A példa programban észlelhető, hogy minden log kétszer szerepel és valóban minden getter kétszer kerül ellenőrzésre.
			Ez csak development mode-ban van így és az Angular csinálja, segíteni felderíteni olyan hibákat, amikor egy
			change detection után történik valaminek a módosítása (és valószínűleg már nem jutna érvényre a UI-n).
		
			Ekkor jelentkezik az ExpressionChangedAfterItHasBeenCheckedError

			Ha a két egymás utáni ellenőrzés nem ugyanazt az értéket mutatja erre figyelmeztet,
			(lehet az adott property valamilyen nem megfelelő helyen történő módosítása a kódban, ami potenciális hiba).
		
		Avoiding Zone Pollution (egyéb optimalizálási lehetőség)

			- Lehetőség van kódrészeket kizárni a change detection ellenőrzése alól.
				A példában egy timer van, ami nem csinál UI-t érintő módosításokat mégis kiváltja az ellenőrzés,
				mivel event-et generál és az Angular nem nézi mi van a benne lévő kódban.

			private zone = inject(NgZone)

			ngOnInit() {
				this.zone.runOutsideAngular(() => {
					setTimeout(() => {
						console.log('Time expired.');
					}, 5000);
				});
			}

		OnPush Change Detection strategy

			Az alapértelmezett, amikor mindent (is) figyel folyamatosan (ChangeDetectionStrategy.Default).

			@Component({
				selector: 'app-messages',
				standalone: true,
				templateUrl: './messages.component.html',
				styleUrl: './messages.component.css',
				imports: [MessagesListComponent, NewMessageComponent],
				changeDetection: ChangeDetectionStrategy.OnPush
			})

			Komponensenként beállítható.

			Az adott komponensre és annak child-jaira 3 esetben fut le change detection
				- ha a komponensben vagy valamelyik child komponensben event váltódik ki
				- ha a komponensben valamelyik input mező módosításra kerül
					- signal-ok esetén is
				- manuális kezdeményezésre

			Végül valószínű minden komponensre érdemes ezt beállítani, ha ez a módszer a választott.

			signal-ok is alkalmazhatóak, azokra is működik.

			Adatok megosztása komponensek közt OnPush esetben

				- Adattárolás service-ben, adatok használata komponensekből service-en keresztül (OnPush beállítás esetén!)

					- signal használatával működik, azaz minden módosítás észlelésre kerül és megjelenik az UI-n
					
					- signal használata nélkül nem működik helyesen 

						Mivel a fenti 3 eset egyike sem teljesül
						- Nem váltódik ki event
						- Nem változik input mező (signal nincs)
						- Nincs manuális change detection kezdeményezés

						Manuális change detection kezdeményezést kell alkalmazni az adatok változása esetén

							Az Angular eszköze erre a célra

								private cdRef = inject(ChangeDetectorRef);
								// Ezt abban a komponensben kell alkalmazni, ahol a változásra reagálni kell az UI-nak (nem a service-ben).

							Kérdés, hogyan szerzünk tudomást az adatok változásáról, mikor kell aktiválni az eszközt?
							A service-ben lévő adatok változásáról kell értesülni az érintett komponensben, ahol kiváltható a change detection.

							Erre a célra az Angular-ba integrált külső library használható: RxJS
							Third party library, de egy elég sokat használt része az Angular-nak.

							A service-ben

							import { BehaviorSubject } from 'rxjs';

								Szolgáltat egy wrapper-t, ami lehetőséget ad feliratkozni rá, 
								lehetőséget biztosítva értesülni a bele csomagolt adat változásáról.

							// RxJS objektum, amire fel lehet majd iratkozni
							// RxJS által menedzselt változók szokásos elnevezése a végén a $ jel.
							// A megfigyelt adat típusának felel meg.
							messages$ = new BehaviorSubject<string[]>([]);
							private messages: string[] = [];

							// Az adat módosítása helyén...
							addMessage(message: string) {
								this.messages = [...this.messages, message];
									
								// RxJS event kiváltása a módosított adat tömb másolatával a paraméterében (next method).
								this.messages$.next([...this.messages]);
							}

							Az adat felhasználási helyén (komponensben)

							  messages: string[] = [];

								ngOnInit() {
									this.messagesService.messages$.subscribe((messages) => {
										this.messages = messages;
										this.cdRef.markForCheck();
									})
								}

								Feliratkozik az RxJS szolgáltatásra
									- Tárolja a paraméterben kapott módosított adat tömböt.
									- Az egész komponenst bejegyzi change detection-re.

								Az ilyen feliratkozások esetén Good Practice kilépéskor a leiratkozás.
								Ez lehetne az ngOnDestroy-ban is vagy az elegánsabb DestroyRef-rel.

							  private destroyRef = inject(DestroyRef);

								ngOnInit() {
									const subscription = this.messagesService.messages$.subscribe((messages) => {
										this.messages = messages;
										this.cdRef.markForCheck();
									})

									this.destroyRef.onDestroy(() => {
										subscription.unsubscribe();
									});
								}

							async Pipe

								A komponensben használt megoldás helyett van egy szintaktikailag egyszerűbb, de a háttérben ugyanez történik.

								Nincs szükség feliratkozásra, leiratkozásra, change detection kiváltásra, helyileg tárolt adatra sem.
								Ezt elvégzi az AsyncPipe.

								@Component({
									...
									imports: [AsyncPipe],
								})

								// Egy helyi referencia a service RxJS objektumára (elnevezése tetszőleges).
								messages$ = this.messagesService.messages$;

								A template-ben ezt a referenciát lehet használni az 'async' pipe-pal kiegészítve.	

								@for (message of messages$ | async; track message) {
									<li>{{ message }}</li>
								}

								Ez a pipe kiegészítés elvégzi az összes fenti műveletet (fel-leiratkozás, change detection megoldás,...)

Zoneless

	- signal-ok alkalmazásával az Angular 18-tól kezdve lehetőség van a zone.js teljes elhagyására
	- A Change Detection továbbra is létezik, csak nincs hozzá kiterjedt mechanizmus, mint a zone.js-ben.
	- A signal-ok maguk gondoskodnak a változás jelzéséről
	- Egyes event-ek szintén jeleznek a change detection-nek, pl. a click event
		Így egy click event handler-ben lévő hagyományos property is működhet az UI-ra bind-olva
	- De pl. egy timer event-beli változás már nem kerül jelzésre (ha nem signal-ról van szó)

	Angular zoneless project

		- angular.json

			A build section-ben
							"polyfills": [
								"zone.js"
							],

			zone.js törlése

							"polyfills": [],

		- main.ts

			import { bootstrapApplication } from '@angular/platform-browser';
			import { provideExperimentalZonelessChangeDetection } from '@angular/core';
			import { AppComponent } from './app/app.component';

			bootstrapApplication(AppComponent, {
				providers: [provideExperimentalZonelessChangeDetection()]
			}).catch((err) => console.error(err));

			- provideExperimentalZonelessChangeDetection importálása.
			- provideExperimentalZonelessChangeDetection függvény futtatása a providers property tömbben.

RxJS (Observables)

	Observables
		- A Stream of Data
		- RxJS Observable-k adatokat tartalmazó eseményeket váltanak ki időről időre
			- Fel lehet iratkozni ezekre az eseményekre (megkapva az adatokat)

		Az előző fejezetben alkalmazott BehaviorSubject is egy observable alkalmazása volt.

		Az observable egy subscriber, amire fel kell iratkozni. 
		Feliratkozás nélkül nem lehet az adatokhoz férni (így ez alapvető)

		Pl.:

		RxJS/interval 
		- Létrehoz egy observable-t, ami a megadott időközönként kivált egy eseményt egy folyamatosan növekvő számmal
		- A hozzáféréshez itt is fel kell iratkozni.

			interval(1000).subscribe({
				next: (val) => console.log(val),
			}); 

		- A feliratkozás paramétere egy objektum (observer object), ami 3 metódust tartalmazhat 
			next: kiváltódik minden új adat érkezésekor
			complete: kiváltódik, ha az observable már nem szolgáltat több adatot
			error: observable működés közbenu hiba esetén hívódik

		- interval esetén csak a next-nek van értelme.
			Amikor csak next van megadva megadható a subscribe után egyetlen függvényként.
			interval(1000).subscribe((val) => console.log(val)); 

		- Good Practice: Leiratkozás

			private destroyRef = inject(DestroyRef);

			ngOnInit() {
				const subscription = interval(1000).subscribe({
					next: (val) => console.log(val),
				}); 

				this.destroyRef.onDestroy(() => {
					subscription.unsubscribe();
				})
			}

			Létezik sok egyéb RxJS függvény, amely observable-t hoz létre.
			De az RxJS függvények zöme már létező observable objektumok vagy azok adatainak befolyásolására használható.
			Ezek a függvények az RxJS operatorok (RxJS Operators)

			RxJS documentation Operators page (https://rxjs.dev/guide/operators)

			Ezek használatához kell a pipe() hívás.
			- map operator, ami az adatok átalakítására használható.

				const subscription = interval(1000).pipe(
					map((val) => val * 2)
				).subscribe({
					next: (val) => console.log(val),
				}); 

			- Kettesével fog számolni

			- több operator is megadható egymás után (vesszővel elválasztva).

		Subjects
			A Subject (pl. BehaviorSubject) egy speciális Observable, de amíg a Subject-ek esetén a változási event kiváltása manuálisan történik,
			addig egy Observable (amihez tipikusan tartozik valamilyen data source) automatikusan teszi.


		fromEvent, exhaustMap

			A fromEvent egy observable-t készít egy event handlerhez.

				import { fromEvent } from 'rxjs';

				const clicks$ = fromEvent(button, 'click');

			exhaustMap: Elnyeli az újabb beérkező adatokat, amíg a belső observable nem végez.
				(a példában a gombnyomás nem eredményez újabb http kérést, amíg az előző kész nincs)

				fromEvent(button, 'click').pipe(
					exhaustMap(() => this.http.post('/api/save', data))
				).subscribe();

			Important rule of thumb
				If you see map, switchMap, mergeMap, concatMap, or exhaustMap — you should NOT call subscribe() inside.

			Comparison with other mapping operators

			Operator	Behavior

			mergeMap	Run all inner Observables concurrently
			switchMap	Cancel previous, keep latest
			concatMap	Queue and run one after another
			exhaustMap	Ignore new values while one is running

			Mental model

			switchMap → “Latest wins”

			concatMap → “Wait your turn”

			mergeMap → “Everyone at once”

			exhaustMap → “Busy — try again later”

	Signals vs Observables

		Az eddigi esetekben az observable-k helyettesíthetők lennének signal-okkal.
		A signal-ok újabb lehetőségek az Angular-ban, korábban az RxJS volt használható adott esetekre.
		A signal-ok megjelenésével vannak olyanok, amelyek nem igénylik az RxJS-t már.

		signal és observable közti különbségek 

			- A signal egy adott értéket tárol és azt bármikor tudja szolgáltatni, bármikor kiolvasható belőle.
				- nem igényel feliratkozást (feliratkozás-szerű viselkedés pl. effect() használatával).
				Jó megoldás:
				- Application állapotok kezelésére

			- Az observable adott időközönként közli az adatot, amely eléréséhez feliratkozás szükséges.
				Jó megoldás:
				- Események kezelésére
				- Adatfolyamokhoz
				- Mindezek aszinkron érkezhetnek az időben

		Adott esetekben nem csak el kell dönteni melyiket használja a program, van amikor konvertálni kell
		egyiket a másikra.

	Converting Signals to Observables
	
		- toObservable: RxJS interoperation függvény erre a célra
			
			import { toObservable } from '@angular/core/rxjs-interop';

			Ott használható, ahol pl. injektálni is lehetne (konstruktorban vagy az osztály mezőjében).
	
			// Konvertálandó signal.
			clickCount = signal(0);
  		
			// A $ a végén a konvencionális jelölés
			// A signal non executed formája kell (nincs () a végén)
			clickCount$ = toObservable(this.clickCount);

			// Innetől ugyanúgy használható, ahogy egy observable...
			ngOnInit() {
			
				const subscription = this.clickCount$.subscribe({
					next: (val) => console.log(`clicked: ${this.clickCount} times`)
				});

				this.destroyRef.onDestroy(() => {
					subscription.unsubscribe();
				})
			}

			// Az Observable next event-et a signal változása automatikusan kiváltja.
			onClick() {
				this.clickCount.update((prev) => prev + 1);
			}

	Converting Observables to Signals

		- toSignal: RxJS interoperation függvény erre a célra
			
			import { toSignal } from '@angular/core/rxjs-interop';

			// Konvertálandó observable;
			interval$ = interval(1000);

			intervalSignal = toSignal(this.interval$);

			Nem kell subscribe/unsubscribe.
			A signal használható, ahogy más signal-ok, pl. a template-ben:

			<p>
				{{ intervalSignal() }}
			</p>

			Egy fontos különbség signal és observable közt, hogy az observable-nek nincs kiindulási értéke,
			amíg a signal-oknak szükségük van a beállítására.
			Így a keletkezett signal először undefined lesz (az UI-n semmi nem jelenik meg) és csak az első
			interval event-nél lesz 0.

			// Ezt egy második paraméter beállításával lehet orvosolni.
			intervalSignal = toSignal(this.interval$, { initialValue: 0 });

			Megjegyzés: A példa programban ez nem működik, mert az initialValue típusa valamiért null | undefined

			Az így konvertált observable nem igényel megszünéskor egyéb műveletet, azt a signal elvégzi (automatic Cleanup).
	
			Ezt át lehet állítani (ha kézzel szeretnénk az observable-t megszüntetni...):
				intervalSignal = toSignal(this.interval$, { initialValue: 0, , manualCleanup: true });

	Building Custom Observables

		Saját observable: egy olyan objektum amire ugyanúgy fel kell iratkozni, ahogy egy "gyári"-ra.

		- Van az RxJS-ben erre egy osztály: Observable

		  customInterval$ = new Observable((subscriber) => {
				setInterval(() => {
					console.log('Emitting new value...');
					subscriber.next({ message: 'New value', value: 1 });
				}, 2000);
			});

			A létrehozáshoz példányosítani kell egy Observable objektumot.
			Az Observable létrehozásánál egy függvény a paraméter.
			A függvény argumentuma a subscriber, ami az az objektum, ami a feliratkozásnál kerül megadásra.
			A függvényben van az observable belső működése, innen lehet meghívni többek közt a subscriber next metódusát.
			A next-nek kötelezően van egy paramétere.

			this.customInterval$.subscribe({
				next: (val) => console.log(val)
			})

			Nem minden observable fut folyamatosan, adott esetben megszüntethető

			Az observable-n belülről hívott complete() függvény:
			- Leállítja a további next hívásokat (még akkor is ha pl., a setInterval tovább pörög és hívná)
			- Meghívja a subscriber complete függvényét, ha definiálva van
			- DE nem végez magától cleanup funkciókat, tehát pl. a timert nekünk kell megszüntetni

			- Ez egy megoldás, ahol a cleanup teendők megadhatók
				Az Observable definícióban átadott függvény visszatérési értéke tartalmazhat egy cleanup függvényt.
				Ezekben az esetekben hívódik:
					- complete hívás az Observable-n belülről
					- az observable unsubscribe híváskor

				const custom$ = new Observable(observer => {
					const id = setInterval(() => observer.next(Math.random()), 1000);

					// Teardown logic (runs on complete OR unsubscribe)
					return () => {
						console.log('Cleanup running...');
						clearInterval(id);
					};
				});

				const sub = custom$.subscribe({
					next: v => console.log(v),
					complete: () => console.log('Completed!') // unsubscribe nem hívja meg csak a belülről hívott complete()
				});			

				// Unsubscribe calls teardown logic...
				sub.unsubscribe();
				// or observer.complete() if you trigger it inside the observable

Sending HTTP Requests and Handling Responses

	HttpClient: Angular service http kérések kezelésére

		Egy adott komponensben:
		
			import { HttpClient } from '@angular/common/http';
			
			private httpClient = inject(HttpClient);

			Önmagában így még nem hozzáférhető a szolgáltatás

			providers: [] megadás szükséges

		Általánosabb megoldás a main-ts-ben beemelni a szolgáltatást (megadni a provider-t):

			import { provideHttpClient } from '@angular/common/http';

			bootstrapApplication(AppComponent, {
				providers: [provideHttpClient()]
			}).catch((err) => console.error(err));

		Http kérés a komponensben:

			ngOnInit() {
				// Adatkérés - get
				this.httpClient
					// Célszerű megadni a kért adat típusát, ami itt egy object: egy "places:" key - "Place[]" tömb value párossal.
					.get<{ places: Place[] }>('http://localhost:3000/places')
					// A get önmagában egy observable-t ad vissza, így arra fel kell iratkozni
					// (a kérés (get) is csak a subscribe hatására megy el)...
					.subscribe({
						next: (resData) => {
							console.log(resData.places);
						},
						complete: () => console.log('completed')
					});
			}

			Ezek a Http függvények (get, put, post,...) observable-t adnak vissza, amelyek egy műveletet végeznek, utána
			egyből hívják a complete() függvényt és befejezik a működésüket.

			Nem szükséges az unsubscribe(), de egy jó gyakorlat lehet minden subscribe-hoz az unsubscrice felvétele a kódban.

			unsubscribe folytonosan érkező adatok (stream) esetén mindenképp szükséges (ahol nem hívódik automatikusan a complete())

		Http kérés konfigurálása

			// get második paramétere lehet egy konfigurációs objektum, ahol az observe key meghatározhatja milyen adatot kérünk.
			// 'response' jelenti, hogy a teljes response objektumot kérjük nem csak az adatot.
			this.httpClient
				.get<{ places: Place[] }>('http://localhost:3000/places', {
					observe: 'response'
				})
				.subscribe({
					next: (response) => {
						// A teljes response-ban a body-ban van az adat.
						console.log(response.body?.places);
					},
					complete: () => console.log('completed')
				});

			// 'events' jelenti, hogy a kérés folyamatában keletkező eseményeket szeretnénk.
			// Ebben az esetben többször meghívásra kerül a next függvény, még a complete() hívás előtt.
			// Az utolsó hívás tartalmazza response-t.
			this.httpClient
				.get<{ places: Place[] }>('http://localhost:3000/places', {
					observe: 'events'
				})
				.subscribe({
					next: (event) => {
						console.log(event);
					},
					complete: () => console.log('completed')
				});

			Az esetek többségében azért csak az adatokra van szükség.

		Response data átalakítása

			// Csak egy lehetőség bemutatása, ami ebben az esetben nem lenne szükséges.
			// pipe alkalmazása, ahol operator függvényekkel lehet befolyásolni az érkező adatot.
			// Jelen esetben a map(), amivel át lehet szabni az adatokat, most a response-ból kiveszi a places-t,
			// így nem a teljes object, csak a Place[] tömb fog a next-nél érkezni...
			this.httpClient
				.get<{ places: Place[] }>('http://localhost:3000/places')
				.pipe(
					map((resData) => resData.places)
				)
				.subscribe({
					next: (data) => {
						this.places.set(data);
						console.log(data);
					},
					complete: () => console.log('completed')
				});
		 	 }

		Adatlekérés folyamatának jelzése

			Pl.: Amíg az adatok lekérdezése van folyamatban:

			isFetching = signal(false);

			ngOnInit() {
				this.isFetching.set(true);

				this.httpClient
					.get<{ places: Place[] }>('http://localhost:3000/places')
					.pipe(
						map((resData) => resData.places)
					)
					.subscribe({
						next: (data) => {
							this.places.set(data);
							console.log(data);
						},
						complete: () => {
							this.isFetching.set(false);
							console.log('completed');
						},
						//error: ,
					});
			}

			template-ben:

				@if (isFetching()) {
					<p class="fallback-text">Fetching available places...</p>
				}

		Hibakezelés

			Megoldható az observer error: property-jénél is, de létezik egy másik megoldás is.
			A pipe-ban "előkezelni" a hibát:

			error = signal('');

			ngOnInit() {
				this.isFetching.set(true);

				this.httpClient
					.get<{ places: Place[] }>('http://localhost:3000/places')
					.pipe(
						map((resData) => resData.places),
						
						// A catchError egy operátor, amelyben megadott függvény paraméterül kapja a hibát és egy observable-t kell visszaadnia.
						catchError((error) => {
							console.log(error);
							// throwError nem egy operator, de egy RxJS függvény, ami egy observable-t ad vissza
							// paramétere (a változatosság kedvéért) egy függvény, amely egy Error objektumot ad vissza.
							return throwError(() => new Error('Something went wrong :('))
						})
					)
					.subscribe({
						next: (data) => {
							this.places.set(data);
							console.log(data);
						},
						complete: () => {
							this.isFetching.set(false);
							console.log('completed');
						},
						error: (err: Error) => {
							this.error.set(err.message);
						},
					});
			}

		Adatküldés a backend-nek

			// put - van egy második paramétere, ahol a küldendő adat van, ez lehetne bármi,
			// jelen esetben egy object a placeId-vel (backend ezt várja).
			// Ez automatikusan alakításra kerül json formára...
			// Mivel a put is egy observable-t készít, a működéshez szükséges a feliratkozás.
			this.httpClient.put('http://localhost:3000/user-places', {
				placeId: selectedPlace.id
			}).subscribe({
				next: (resData) => console.log(resData)
			});

		Http műveletek service-be helyezése

		- Célszerű a .subscribe előtti részt kirakni a service-be. Onnan egy observable jön vissza és a feliratkozás
			egyedi részletei pedig a komponensben maradnak...

	Service:
 
		loadAvailablePlaces() {
			return this.fetchPlaces('http://localhost:3000/places', 'Something went wrong :(');
		}

		private fetchPlaces(url: string, errorMsg: string) {
			return this.httpClient
				.get<{ places: Place[] }>(url)
				.pipe(
					map((resData) => resData.places),
					catchError((error) => {
						console.log(error);
						return throwError(() => new Error(errorMsg))
					})
				)
		}

	Component:
			
		ngOnInit() {
			this.isFetching.set(true);

			const subscription = this.placesService.loadAvailablePlaces().subscribe({
				next: (data) => {
					this.places.set(data);
					console.log(data);
				},
				complete: () => {
					this.isFetching.set(false);
					console.log('completed');
				},
				error: (err: Error) => {
					this.error.set(err.message);
				},
			});

			this.destroyRef.onDestroy(() => subscription.unsubscribe());
		}

		- A leiratkozás is a komponensben marad (DestroyRef)
			- Habár ebben az esetben nem szükséges a leiratkozás mivel egy http get kérés egyszer végrehajtódik és
				hívja a complete() függvényt.
			- Hibát nem okoz és egy Angular-os Good Practice a destroyRef ilyen használata observer-ek esetén.

		- Lekérdezett adatok lokális tárolása a service-ben
			- Mivel a subscription a komponensben van, az adatok alapból oda érkeznek (ott van lehetőség tárolni).
			- pipe(tap()) - tap operator alkalmazásával meg tudjuk csapolni az observable-t még a subscription előtt a service-ben.

			loadUserPlaces() {
				return this.fetchPlaces('http://localhost:3000/user-places', 'Something went wrong :(')
					.pipe(tap({
						next: (userPlaces) => this.userPlaces.set(userPlaces),
					}));
			}

		- Módosítások lokális kezelése (Optimistic Updating).
			Pl. egy új elem hozzáadása: 
				- Hozzáadás Http kérés kiküldése
				- Új elem hozzáadása a lokális tömbhöz.
				- Nem lehetünk biztosak, de feltételezzük, hogy a Http kérés hibátlanul lefut és a UI már ezt jelzi.

			Hibakezelési lehetőség:
				.pipe(catchError(...)) - catchError operator használata a put-nál.
				- A lokálisan tárolt tömb rollback-elése hiba esetén.
				- Figyelés, hogy már meglévő elemet ne adjon hozzá mégegyszer

				addPlaceToUserPlaces(place: Place) {
					const prevPlaces = this.userPlaces();

					if (!prevPlaces.some((p) => p.id === place.id)) {
						this.userPlaces.update(prevPlaces => [...prevPlaces, place]);
					}
					
					return this.httpClient.put('http://localhost:3000/user-places', {
						placeId: place.id
					}).pipe(
						catchError(() => {
							this.userPlaces.set(prevPlaces);
							this.errorService.showError('Failed to add new item :(');

							return throwError(() => new Error('Failed to add new item :('))
						})
					);
				}

	Http interceptors (elfogó)

		Http műveletek végrehajtásába való betekintés, módosítás...

		main.ts

			import { HttpHandlerFn, HttpRequest, provideHttpClient, withInterceptors } from '@angular/common/http';

			// Függvény, ami a Http kommunikáció során hívódik.
			function loggingInterceptor(
				request: HttpRequest<unknown>, // eredeti request
			  next: HttpHandlerFn // Kérést továbbító függvény, ha csak meghívjuk next(request), akkor nem lesz látható hatása az interceptor-nak.
			) {
				
				// Kimenő kérések elkapása...
				console.log('[Outgoing request');
				console.log(request);
				return next(request);
			}

      // withInterceptors megadása a provideHttpClient paraméterében...
			bootstrapApplication(AppComponent, {
				providers: [provideHttpClient(withInterceptors([loggingInterceptor]))]
			}).catch((err) => console.error(err));


			Az elkapott Http kérés módosítható:

				const req = request.clone({
					headers: request.headers.set('X-DEBUG', 'TESTING')
				});
				
				return next(req);

			Érkező válaszok elkapása

				function loggingInterceptor(
					request: HttpRequest<unknown>, // eredeti request
					next: HttpHandlerFn // Kérést továbbító függvény, ha csak meghívjuk next(request), akkor nem lesz látható hatása az interceptor-nak.
				) {
					
					// Kimenő kérések elkapása...
					console.log('[Outgoing request');
					console.log(request);
					
					// Érkező válaszok elkapása.
					// .pipe alkalmazása (egy .subscribe lezárná az observable láncolatot, az itt nem lenne jó).
					// tap operátorral "feltűnés nélkül" belenézhetünk az érkező adatba...
					return next(request).pipe(
						tap({
							next: event => {
								if (event.type === HttpEventType.Response) {
									console.log('Incoming response');
									console.log(event.status);
									console.log(event.body);
								}
							}
						})
					);
				}

Form kezelés (Template-driven / Reactive Forms)

	A korábban használt módszerek valamelyike is elegendő lehet form elemek kezelésére (two-way binding, template variables),
	de összetettebb esetekben szükség lehet ez a két fejlettebb módszer szolgáltatásaira (error handling, validation,...).

	Template-driven esetben a template tartalmaz minden form elemet.
	Reactive esetben ts kódban vannak összeállítva a form elemei és az van csatolva a template-be...

	Template-driven Forms

		Pl.: HTML form

		<form>
			<h2>Login</h2>

			<div class="control-row">
				<div class="control no-margin">
					<label for="email">Email</label>
					<input id="email" type="email" />
				</div>

				<div class="control no-margin">
					<label for="password">Password</label>
					<input id="password" type="password" />
				</div>

				<button class="button">Login</button>
			</div>
		</form>

		A cél a fenti form definíció kiegészítése olyan Angular elemekkel, amelyek hozzáférést biztosítanak számára.

		Szükséges a FormsModule importálása a komponensben

			import { FormsModule } from '@angular/forms';

			@Component({
				...
				imports:[FormsModule],
				...
			})
			export class LoginComponent {}

			Ha ez importálva van, a <form> elemeket az Angular már detektálja...

		ngModel direktíva

			Korábban a two-way binding-nál használtuk, de nem csak arra való
			
			Beregisztrál egy input elemet az Angular számára (ez lehet textarea, select element,...)
			<input id="email" type="email" ngModel/>

			ngModel megadása esetén az input elemnek rendelkeznie kell name attribútummal (ez lehet bármi).

			<input id="email" type="email" name="email" ngModel />

		Jelen beállításokkal az Angular még nem fér hozzá a form-hoz.
		
		Alkalmazható a template variable (ahogy korábban). 
		A template variable kaphat értéket, jelen esetben egy 
		
		<form #form="ngForm">  
			- ngForm egy speciális Angular azonosító

		Így az alapértelmezett HTMLFormElement típus helyett NgForm típusú lesz.

		(ngSubmit), ahogy korábban a button click-re hívódik automatikusan az Angular által.

		<form #form="ngForm" (ngSubmit)="onSubmit(form)">

		Az onSubmit paramétere egy (az Angular által összeszerkesztett) NgForm objektum, 
		ami tartalmazza az egész form aktuális adatait hozzáférést biztosítva azokhoz.

		onSubmit(form: NgForm) {
			console.log(form);
		}

		Input adatok kibontása az NgForm objektumból
		(használható lenne a two-way binding ugyanúgy [(ngModel)], de a példa kedvéért most nincs)

			onSubmit(formData: NgForm) {

				// A 'form' key (property) 'value' membere egy object-ben tartalmazza az 'email' és 'password' input mezők tartalmát.
				const enteredEmail = formData.form.value.email;
				const enteredPassword = formData.form.value.password;
				
				console.log(enteredEmail, enteredPassword);
			}

		Input adatok validációja

			Attribútumok adhatóak az input elemekhez, amelyek meghatározzák, hogy milyen adatok az elfogadottak.
			Pl.: required, email, minlength, maxlength, pattern

			<div class="control no-margin">
				<label for="email">Email</label>
				<input id="email" type="email" name="email" ngModel required email />
			</div>

			<div class="control no-margin">
				<label for="password">Password</label>
				<input id="password" type="password" name="password" ngModel required minlength="6"/>
			</div>

			Attribútumok (kezelésüket átveszi az Angular, még ha azok nevei megegyeznek standard HTML attribútumokkal)

			A submit hatására nem keletkezik automatikusan hiba, csak az NgForm objektum tartalmából lehet kiszűrni.

			onSubmit(formData: NgForm) {
				
				// Létezik a párja 'valid' property is...
				if (formData.form.invalid) {
					return;
				}

				const enteredEmail = formData.form.value.email;
				const enteredPassword = formData.form.value.password;
				
				console.log(enteredEmail, enteredPassword);
			}

			Ez egy globális ellenőrzés a form-ra, hogy valami nem OK.
			Ennél részletesebben is tárolásra kerül, hogy pontosan mi az, ami nem valid.

			Ezt kihasználva részletes hibaüzenet adható a hibáról, célszerűen a template-ben megvalósítva.

			Globális ellenőrzés:

				@if (form.form.invalid) {
					<p class="control-error">
						Invalid values detected. Please check your input.
					</p>
				}

				Megjegyzés:
					A template-ben nem minden elem lesz stabilan elérhető a form variable-n keresztül.
					Pl. form.form.controls['email'] a gyakorlatban létezik, de az első rendereléskor még nem épül
					fel a struktúra, amiben benne van, így hibajelzés lehetséges.

					Ebben az esetben célszerű az egyes input elemehez külön template variable hozzáadása.

						<input id="email" type="email" name="email" ngModel required email #email="ngModel"/>
						<input id="password" type="password" name="password" ngModel required minlength="6" #password="ngModel"/>

					Értékként az ngModel-t kell adni (ahogy a form esetén az ngForm).

			Részletesebb ellenőrzés:

				@if ( email.touched && email.dirty && email.invalid) {
					<p class="control-error">
						Invalid email address entered.
					</p>
				}

				@if ( password.touched && password.dirty && password.invalid) {
					<p class="control-error">
						Invalid password.
					</p>
				}

				touched: Az input elembe belépés és egy kilépés történt (fókuszba került és elvesztette azt).
				Ezzel az érhető el, hogy kiindulásként nem látszik a hibaüzenet, csak valamilyen user aktivitás után.


			Validáció eredményének jelzése css-en keresztül

				Az Angular az általa készített HTML-ben bizonyos class-okat hozzáad az input elemekhez a validáció eredményétől függően.

				Lehetséges class-ok pl.:
					ng-pristine : nem történt még bevitel az input mezőbe (tartalma még az eredeti (többnyire üres))
					ng-invalid : input mező invalidnak van minősítve
					ng-touched : user már egyszer kijelölte az imput mezőt és átlépett róla
					ng-dirty : az input mező tartalma már változott (még ha közben visszaállításra is került)

				Ezek a class-ok használhatóak vizuálisan megjeleníteni a hibákat. Ehhez a css-t kell felkészíteni igény szerint...

					.control:has(.ng-invalid.ng-touched.ng-dirty) label {
						color: #f98b75;
					}

					input.ng-invalid.ng-touched.ng-dirty {
						background-color: #fbdcd6;
						border-color: #f84e2c;
					}

		Template driven esetben célszerű a template-ben megoldani, amit lehet, de szükség lehet a komponensbeli megoldásokra is

			Pl. egy submit után visszaállítani a form-ot alaphelyzetre

				formData.form.reset();

			Ez nem csak az input mezők tartalmát, hanem a jellemzőit is alaphelyzetre állítja (valid-invalid, touched,...)
			Mivel ezek az egyéb tulajdonságok is hozzáférhetőek az NgForm objektumon keresztül, azok kézi beállítására is van lehetőség.

				Pl.: formData.form.markAsDirty();

			Igény lehet a form-ra bevitt tartalom megőrzésére abban az esetben ha az oldal valamiért újratöltésre kerül.
			Ehhez el kell tárolni a bevitt tartalmat és újratöltésnél visszaállítani.

			A fenti esetekben a form adatai az onSubmit-ban paramterként érkeztek, ebben az esetben ettől függetlenül is el kell tudni érni őket.

			// A template variable segítségével "behúzzuk" a komponensbe a form-ot (NgForm)...
			// viewChild() egy signal-t ad vissza.
			// required, hogy ne kelljen ? a használathoz
			private form = viewChild.required<NgForm>('form');

			Szükség van értesülni a változásokról, hogy tárolni tudjuk az aktuális tartalmat.
			A korábban tárgyalt afterNextRender-t használjuk erre a célra, ami egyszer hívódik a következő újra rendereléskor.
			A tárolás a localStorage-ba történik, ami egyszerűbb alkalmazásoknál jó lehet.

			// unsubscribe-okhoz...
			private destroyRef = inject(DestroyRef);

			constructor() {
				afterNextRender(() => {
					// Az NgForm szolgáltat egy observable-t (valueChanges), ami jelzi a módosításokat, erre fel kell iratkozni.
					this.form().valueChanges?.subscribe({
						next: (value) =>
							window.localStorage.setItem('saved-login-form', JSON.stringify({ email: value.email }))
					});

		   			this.destroyRef.onDestroy(() => subscription?.unsubscribe());
				})
			}

			A fenti megoldásban a tárolás minden billentyű leütésre megtörténik, amin lehet optimalizálni.
			Az observable-khöz rengeteg funkció elérhető a pipe-on keresztül (operators).
			Az egyik a debounceTime, ami adott idővel késlelteti az esemény kiváltódását és ha időközben újra kiváltódik,
			akkor csak az utolsó kerül meghívásra.

   			// 500 msec a késleltetés...
			const subscription = this.form().valueChanges?.pipe(debounceTime(500)).subscribe({

			Tárolt adatok visszaállítása a komponens betöltődésekor

				Több úton is elérhetőek az input elemek: 
				
				this.form().setValue({
					email: savedEmail,
					password: ''
				});

				this.form().controls['email'] = savedEmail;

				Viszont hibát kapnánk, mivel az elemek ebben az esetben még nem állnak rendelkezésre.
				A setTimeout lehet megoldás...

				constructor() {
					afterNextRender(() => {

						const savedForm = window.localStorage.getItem('saved-login-form');

						setTimeout(() => {
							if (savedForm) {
								const loadedForm = JSON.parse(savedForm);
								const savedEmail = loadedForm.email;

								this.form().controls['email'] = savedEmail;
							}
						}, 1);

						const subscription = this.form().valueChanges?.pipe(debounceTime(500)).subscribe({
							next: (value) =>
								window.localStorage.setItem('saved-login-form', JSON.stringify({ email: value.email }))
						})

						this.destroyRef.onDestroy(() => subscription?.unsubscribe());
					})
				}

	Reactive Forms

		A komponens ts kódjában vannak összeállítva a form elemei, amelyek onnan kerülnek csatolásra a template megfelelő eleméhez.

			Kell egy FormGroup property a komponensbe, amelynek konstruktorában kell felsorolni az összes érintett form input elemet.
			Az egyes elemek alap esetben FormControl típusúak, amelyek konstruktorában lehetőség van a default érték megadására.
			Itt elképzelhető egyéb (beágyazott) FormGroup is.

			export class LoginComponent {
				form = new FormGroup({
					email: new FormControl(''),
					password: new FormControl('')
				});

				onSubmit() {
					// Példa egy érték elérésére...
					console.log(this.form.value.email);
				}
			}

			Egyes elemek template-hez rendelése.

				Szükséges import a komponensben:
				  imports: [ReactiveFormsModule],

				Template kiegészítések (property bindings)

					[formGroup] - FormGroup binding
					[formControl] - Control binding
					
					A control binding helyett használható a név megadása
						formControlName="password"

						- Ez itt a FormGroup-ban megadott név, ami eltérhet a template-ben használt névtől.

					<form [formGroup]="form">
						<h2>Login</h2>

						<div class="control-row">
							<div class="control no-margin">
								<label for="email">Email</label>
								<input id="email" type="email" [formControl]="form.controls.email" />
							</div>

							<div class="control no-margin">
								<label for="password">Password</label>
								<input id="password" type="password" formControlName="password"/>
							</div>

							<button class="button">Login</button>
						</div>
					</form>

				Submit ugyanúgy megy, mint egyéb esetben

					<form [formGroup]="form" (ngSubmit)="onSubmit()">

				Adatok elérése

					onSubmit() {
						console.log(this.form);
						const enteredEmail = this.form.value.email;
						const enteredPassword = this.form.value.password;
						console.log(enteredEmail, enteredPassword);
					}

		Adatok validálása

			Template-driven esetben input mező megkötések a template-ben voltak (required,...)
			
			Itt a ts kódban kell validátorokat hozzárendelni az egyes control-okhoz.

				A FormsControl konstruktor második paramétere lehetne közvetlenül egy Validator tömb
					email: new FormControl('', [])

				Lehet azonban egy konfigurációs objektum is, amelynek egyik eleme a validators.
 				A konfigurációs objektum tartalmazhat egyéb beállításokat is...

				export class LoginComponent {
					form = new FormGroup({
						email: new FormControl('', {
							validators: [
								Validators.email,
								Validators.required
							],
						}),
						password: new FormControl('', {
							validators: [
								Validators.required,
								Validators.minLength(6)
							],
						}),
					});

			Ezek után a form elemeihez az Angular ugyanúgy szolgáltatja az állapot jelzőket.

				get emailIsInvalid() {
					return (
						this.form.controls.email.touched &&
						this.form.controls.email.dirty &&
						this.form.controls.email.invalid);
				}

				get passwordIsInvalid() {
					return (
						this.form.controls.password.touched &&
						this.form.controls.password.dirty &&
						this.form.controls.password.invalid);
				}

				Amelyek a template-ben használhatók (getter-en keresztül vagy akár közvetlenül is (form-on keresztül))

					@if (emailIsInvalid) {
						<p class="control-error">
							Email address is incorrect.
						</p>
					}
					@if (passwordIsInvalid) {
						<p class="control-error">
							Password is incorrect.
						</p>
					}

				A szolgáltatott class beállítások is ugyanúgy működnek, ahogy template-driven esetben (ng-pristine,...)

		Custom validátorok készítése

			Template-driven esetben ez egy kicsit nehézkes:
				https://angular.dev/guide/forms/form-validation#adding-custom-validators-to-template-driven-forms

			Reactive Form esetén van beépített módszer

			Kell egy függvény, amely visszatérési értéke null, ha az érték valid,
			egyéb esetben valami (ez konvencionálisan valamilyen beszédes objektum).

				function mustContainQuestionMark(control: AbstractControl) {
					if (control.value.includes('?')) {
						return null;
					}
					return { doesNotContainQuestionMark: true };
				}

			Amit meg kell adni a validátorok közt:

				password: new FormControl('', {
					validators: [
						Validators.required,
						Validators.minLength(6),
						mustContainQuestionMark
					],
				}),

		Async Validators

			Megadhatóak aszinkron működésű validátorok is, amely szintén egy-egy függvény lehet.
			Visszatérési értékük minden esetben egy Observable.
			Az Observable-ból származó eredmény valid esetben null, egyébként egy hiba leíró objektum, ahogy a szinkron esetben.
			A függvény lehet pl. egy Http kérés, ahol a szerverről érkezik az validálás (egy ilyen kérés pedig aszinkron).

				Az of() egy azonnal emittáló Observable, amely a paraméterében megadott értékkel emittál és azonnal befejezi a működést (complete).

				import { of } from 'rxjs';

				function emailIsUnique(control: AbstractControl) {
					if (control.value !== 'test@test.hu') {
						return of(null);
					}
					return of({notUnique: true});
				}

				Ezt a függvényt kell hozzáadni az asyncValidators tömbhöz

				email: new FormControl('', {
					validators: [
						Validators.email,
						Validators.required
					],
					asyncValidators: [emailIsUnique]
				}),

		Form adatainak megőrzése Reactive Form esetén

			Használható az OnInit, itt nincs szükség az afterNextRender-re a konstruktorban, ahogy tempalte-driven esetben,
			mivel itt helyben vannak a form elemei előállítva, így azok már léteznek az ngOnInit hívásakor.
			
			ngOnInit() {
				const subscription = this.form.valueChanges?.pipe(debounceTime(500)).subscribe({
					next: value => {
						window.localStorage.setItem(
							'saved-login-form', 
							JSON.stringify({ email: value.email })
						);
					}
				});

				this.destroyRef.onDestroy(() => subscription.unsubscribe);
			}

		Megőrzött adatok visszaállítása

			Történhet az OnInit-ben:

				ngOnInit() {
					const savedForm = window.localStorage.getItem('saved-login-form');

					if (savedForm) {
						const loadedForm = JSON.parse(savedForm);
						this.form.patchValue({
							email: loadedForm.email
						})
					}

					const subscription = this.form.valueChanges?.pipe(debounceTime(500)).subscribe({
						next: value => {
							window.localStorage.setItem(
								'saved-login-form', 
								JSON.stringify({ email: value.email })
							);
						}
					});

					this.destroyRef.onDestroy(() => subscription.unsubscribe);
				}

				form értékeinek beállítása
					Eddigi módszerekkel:
						this.form.setValue({ email: 'm', password: 'p'});
						this.form.controls.email.setValue( 'm' );

					Még egy lehetőség:
						this.form.patchValue({ email: 'm' });

						Itt rész adatok megadása is lehetséges és csak azok kerülnek update-re.
						A fenti form.setValue esetén a teljes objektum megadása szükséges (password is)

			Egy másik érdekes lehetőség:

				A komponens osztály kódján kívül, ami lefut egyszer, amikor a komponens először inicializálásra kerül

					let initialEmailValue = '';
					const savedForm = window.localStorage.getItem('saved-login-form');

					if (savedForm) {
						const loadedForm = JSON.parse(savedForm);
						initialEmailValue = loadedForm.email;
					}

					@Component({
						selector: 'app-login',
						...

				Az initialEmailValue tartalmazza a korábban mentett adatot vagy ''.

				A FormGroup/FormControl definícióban pedig közvetlenül beállítható

					form = new FormGroup({
						email: new FormControl(initialEmailValue, { ...

				Megjegyzés: Lehet olyan eset, amikor ez a megoldás nem célszerű, pl. server side pre-rendering esetén,
					de, ha az nincs, akkor tökéletes.

		Komplex form kezelése

			Választó lista (combobox)

				<select id="role" name="role" formControlName="role">
					<option value="student">Student</option>
					<option value="teacher">Teacher</option>
					<option value="employee">Employee</option>
					<option value="founder">Founder</option>
					<option value="other">Other</option>
				</select>

				FormGroup-on belüli definíciója (meghatározható a konkrét típus, ami itt egy union):

					role: new FormControl<'student' | 'teacher' | 'employee' | 'founder' | 'other'>('student', {
						validators: [Validators.required]
					}),

			Egymásba ágyazott FormGroup-ok
				A form-on lévő adatok csoportosítására szolgál.
				Az összetartozó adatokat egy-egy külön FormGroup-ba lehet szervezni.

				Pl. cím adatok:

		    address: new FormGroup({
					street: new FormControl('', { validators: [Validators.required] }),
					number: new FormControl('', { validators: [Validators.required] }),
					postalCode: new FormControl('', { validators: [Validators.required] }),
					city: new FormControl('', { validators: [Validators.required] }),
				}),

				Az új csoport nevét meg kell adni a template-ben (mindig a csoportot befoglaló elemhez)
				A megadás történhet teljes névvel [formGroup]="form.controls.address" vagy a rövidebb formGroupName beállításával.

		    <fieldset formGroupName="address">
					<legend>Your Address</legend>

					<div class="control-row">
						<div class="control">
							<label for="street">Street</label>
							<input type="text" id="street" name="street" formControlName="street"

				Az elemekhez való hozzáférés így kap egy plusz "szintet": 
					this.form.value.address?.city

			Form Arrays

				Példa: Három checkbox tömbösítése.

		    source: new FormArray([
					new FormControl(false),
					new FormControl(false),
					new FormControl(false),
				]),

 				Az egyes FormControl-ok nem kapnak egyedi nevet, az indexükkel lehet rájuk hivatkozni...

				A beágyazó elemnél kell a formArrayName megadás, az egyes elemeknél pedig az index van a formControlName-nél.

				<fieldset formArrayName="source">
					<legend>How did you find us?</legend>
					<div class="control">
						<input type="checkbox" id="google" name="acquisition" value="google" formControlName="0"/>
						<label for="google">Google</label>
					</div>

					<div class="control">
						<input type="checkbox" id="friend" name="acquisition" value="friend" formControlName="1"/>
						<label for="friend">Referred by friend</label>
					</div>

					<div class="control">
						<input type="checkbox" id="other" name="acquisition" value="other" formControlname="2"/>
						<label for="other">Other</label>
					</div>
				</fieldset>

			Több input mező együttes validálása (Multi-Input Validators)

				Pl. ha a password ás a confirmPassword egyezését szeretnénk ellenőrizni.

				Közös FormGroup-ba kell őket helyezni és a FormGroup-nak is van konfigurációs objektum paramétere, 
				ahol validátorok megadása lehetséges.

		  	passwords: new FormGroup({
					password: new FormControl('', {	
						validators: [Validators.required,	Validators.minLength(6)]
					}),
					confirmPassword: new FormControl('', {
						validators: [Validators.required,	Validators.minLength(6)]
					})
				}, {
					validators: [equalValues],
				}),

				Megadható egy saját validátor függvény:
					Az AbstractControl nem elég specifikus típus, ahhoz hogy el lehessen érni a password mezőket,
					ezért egy általánosabb úton veszi az értéküket (control.get()).

					function equalValues(control: AbstractControl) {

						const password = control.get('password')?.value;
						const confirmPassword = control.get('confirmPassword')?.value;

						if (password === confirmPassword) {
							return null;
						}

						return { notEqual: true };
					}

				FONTOS!
					A FormGroup validálás eredménye jelen esetben a megjelenítésen nem látszik.
					A láthatósághoz az Angular-nak hozzá kell adnia a class-okat az aktuális HTML elemekhez (ng-invalid,...)
					Ez megtörténik, de nem az egyes input elemekhez, hanem a befoglaló elemhez.

					Emiatt ki kell egészíteni a css-t (+ [formgroupname] sorok)

					[formgroupname].ng-invalid.ng-touched.ng-dirty label,
					.control:has(.ng-invalid.ng-touched.ng-dirty) label {
						color: #f98b75;
					}

					[formgroupname].ng-invalid.ng-touched.ng-dirty input,
					input.ng-invalid.ng-touched.ng-dirty {
						background-color: #fbdcd6;
						border-color: #f84e2c;
					}

			Validator factory

				A validátorok közt van olyan, ahol nem a függvény neve van megadva, hanem egy függvényhívás.
				pl. Validators.minlength(6)

				Ez saját validátor függvényeknél is lehetséges.
				Mivel a validators tömbben egy adott formájú függvényt kell megadni, így oda jó egy olyan függvény
				(factory függvény), ami ilyen formájú függvényt ad vissza.

				// Factory
				function equalValues(controlName1: string, controlName2: string) {
					// Validator-nak megfelelő formájú függvény a visszatérési érték...
					return (control: AbstractControl) => {
						const val1 = control.get(controlName1)?.value;
						const val2 = control.get(controlName2)?.value;

						if (val1 === val2) {
							return null;
						}

						return { notEqual: true };
					}
				}

				Validator megadádsa ebben az esetben:

					validators: [equalValues('password', 'confirmPassword')],

Routing

	Az Angular applikációk legtöbb esetben single page applikációk.
	Ez azt jelenti, hogy egyetlen HTML oldal kerül lekérésre a szerverről (pl. index.html).
	Még egy bonyolult szerkezetű app is single page lehet.
	
	Ettől még kellenek váltások a megjelenített tartalomban, amihez az Angular biztosít egy fajta
	kliens oldali routing-ot. Az Angular kezel mindent, az url-ben lévő dolgokat, ...

	Ennek alkalmazásával összetett UI szerkezetek is kezelhetőek, lehetőség van adatok átadására, vételére.
	A route-ok közti váltásokkal nem kerül lekérésre újabb oldal, a megjelenítendő komponensek a routing
	alkalmazásával szabályozhatóak (nem pedig a láthatóságuk ki-be kapcsolgatásával).

	Routing hozzáadása (standalone eset)

		main.ts

			import { bootstrapApplication } from '@angular/platform-browser';
			import { provideRouter } from '@angular/router';

			import { AppComponent } from './app/app.component';
			import { TasksComponent } from './app/tasks/tasks.component';

			// bootstrapApplication második paraméterében (konfigurációs objektum) providers-hez router meghatározása (provideRouter())
			bootstrapApplication(AppComponent, {
				providers: [provideRouter([
					{
						path: 'tasks', // pl. localhost:4200/tasks
						component: TasksComponent
					}
				])]
			}).catch((err) => console.error(err));

		Ezt szét szokás szedni külön fájlokba:

		app.routes.ts
			A routing-ban érintett komponensek felsorolása (tömb)

			import { Routes } from "@angular/router";
			import { TasksComponent } from "./app/tasks/tasks.component";

			export const routes: Routes = [
				{
					path: 'tasks', // pl. localhost:4200/tasks
					component: TasksComponent,
				},
			];

		app.config.ts
			Konfigurációs objektum külön fájlba. Ez a teljes app konfiguráció, nem csak a routing adatokat tartalmazhatja.

			import { ApplicationConfig } from "@angular/core";
			import { provideRouter } from '@angular/router';
			import { routes } from "./app.routes";

			export const appConfig: ApplicationConfig = {
				providers: [
					provideRouter(routes)
				]
			}

		main.ts (átalakítva)

			import { bootstrapApplication } from '@angular/platform-browser';
			import { AppComponent } from './app/app.component';

			import { appConfig } from './app.config';

			bootstrapApplication(AppComponent, appConfig).catch((err) => console.error(err));

		Egy legenerált app már így fogja tartalmazni...

		Az így definiált routes-ok közül egyik lehet kiválasztva (a url-ben látszik).
		Ahhoz, hogy a route által mutatott tartalom megjelenjen egy speciális direktívát kell elhelyezni a template-ben.

			<div>
				<router-outlet />
			</div>

		Amikor az url nem tartalmaz külön route definíciót, arra egy külön route megadása lehetséges (path:  '')

			export const routes: Routes = [
				{ path: '', component: NoTaskComponent },
				{ path: 'tasks', component: TasksComponent },
			];
	

			Pl.:
				path: '' -> url -> localhost:4200
				path: 'tasks' -> url -> localhost:4200/tasks

		Routes-ok közti váltás

			Link tag <a> használható

			<a routerLink="/tasks">...</a>

			routerLink Angular direktíva (habár nem ng-vel kezdődik), módosítja az <a> tag viselkedését.
			Nem  tölti le újra az oldalt, hanem a routing-nak megfelelően rendereli újra a tartalmat.

			Komponensben imports: [RouterLink] szükséges

			Az aktuális route jelölése a link tag-ben

		  <a routerLink="/tasks" routerLinkActive="selected">...</a>

			routerLinkActive Angular direktíva.
			Megadható egy css class, ami az active link-re érvényes.

			Komponensben imports: [RouterLink, RouterLinkActive] szükséges

		Dynamic Routes

		  { path: 'users/:userId', component: UserTasksComponent },

			A kettőspont utáni rész a dinamikus tartalom, ami a programból módosítható (a kettőspont csak szintaktikai okból kell, nem lesz a része a path-nak).
				- ún. path paraméter vagy paraméterek

		  <a [routerLink]="'/users/' + user().id" routerLinkActive="selected">

				ahol a user a példában egy imput signal

					user = input.required<User>();

			Egy másik megadási móddal:

			  <a [routerLink]="['/users', user().id]" routerLinkActive="selected">

				A tömb elemeit konkatenálja a szükséges '/' jelek közbeiktatásával

			A fenti megoldásban az egyes linkek megkülönböztetésre kerülnek a dinamikus rész segítségével.
			Mégha mindegyik ugyanarra a linkre (komponensre) mutat (users). Így az aktív link jelzése korrekt lesz.
			De egyenlőre másra nincs használva.

			A link-ben szereplő dinamikus rész elérése az adott komponensben

				A { path: 'users/:userId', component: UserTasksComponent } részben szereplő userId dinamikus tartalmáról van szó.

				Ehhez a komponensben kell egy azonos nevű input signal:

				  userId = input.required<string>();

				A név alapján az Angular összekapcsolja őket, amihez szükséges az alábbi konfigurációs beállítás:
					- withComponentInputBinding()

					export const appConfig: ApplicationConfig = {
						providers: [
							provideRouter(routes, withComponentInputBinding())
						]
					}

				Hozzáférés @Input segítségével (így is működik a név szerinti összekapcsolás)

					@Input({required: true}) userId!: string;

					Ez lehet egy setter-es @Input is

						@Input()
						set userId(uid: string) {
							console.log(uid);
						}

				+1 hozzáférési mód (a teljesség kedvéért)

					Kell egy speciális service:

					  private activatedRoute = inject(ActivatedRoute);

						Ez a service tartalmaz számos objektumot, amiből információk nyerhetőek (ebből jónéhány observable).
							- Ebből az egyik observable a paramMap, amiből a path paramétereket lehet megkapni.

					    this.activatedRoute.paramMap.subscribe({
								next: paramMap => console.log(paramMap.get('userId'))
							});

						Az ActivatedRoute-nak van egy snapshot objektuma is.
							- Ebben hasonló mezők vannak, mint a fő objektumban, de nem observable-k, tehát bizonyos adatokhoz
								közvetlen hozzáférést biztosítanak (subscription nélkül), viszont csak abban az egy pillanatban
								(és nem értesülnek a változásukról).

								this.activatedRoute.snapshot.paramMap.get('userId');

						Megjegyzés:
							Van egy params observable is, ami ugyanazokat az adatokat tartalmazza.
							A paramMap-on keresztüli elérés a "korszerűbb"...

			Beágyazott route-ok (Nested Routes)

				export const routes: Routes = [
					{ path: '', component: NoTaskComponent },
					{
						path: 'users/:userId',
						component: UserTasksComponent,
						children: [
							{ path: 'tasks', component: TasksComponent },
							{ path: 'tasks/new', component: NewTaskComponent }
						]
					},
				];

				A children route-ok megjelenítéséhez szintén kell egy <router-outlet/> a parent route komponensében
				(a példában a UserTasksComponent-ben).

				Ott mindig az aktív jelenik meg (szintén lehet több route felsorolva)

				- localhost:4200/users/u1/tasks
				- localhost:4200/users/u1/tasks/new

				Relatív elérési utak a link-ben

					Pl.: A UserTasksComponent az aktív a localhost::4200/users/u2 url-legalább

					Ha innen van egy újabb link valamelyik child route-ra, akkor nem szükséges az egész path-t megadni.

					<a routerLink="tasks/new">Add Task</a>

				A link-ben szereplő dinamikus rész elérése a beágyazott route komponensében

					Szintén kell egy azonos nevű signal a komponensen belül:

				  	userId = input.required<string>();

				Ebben az esetben nem elég a withComponentInputBinding() konfigurációs beállítás.
				Child route komponens esetén nem megy át a userId, ehhez kell egy újabb konfig...

					- withRouterConfig({ paramsInheritanceStrategy: 'always' })

					export const appConfig: ApplicationConfig = {
						providers: [
							provideRouter(routes, withComponentInputBinding(), withRouterConfig({
								paramsInheritanceStrategy: 'always',
							}))
						]
					}

			Route link shortcuts

				Egy adott route útvonalról egyet visszalépni a routerLink="../" megadásával lehet.
				Ez hasznos pl. a Task Listás példában a Create Task-ból Cancel-le való visszalépésre

				<a routerLink="../">Cancel</a>

			Programból vezérelt route navigáció

				Kell egy erre való service:
					private router = inject(Router);

					// A navigate paramétere egy tömb, amely elemeit egy path-ba fűzi össze...
					onSubmit() {
						this.router.navigate(['/users', this.userId, 'tasks']);
					}

					A navigate-nek lehet második paramétere, egy konfigurációs objektum.
					Ebben sokféle beállítás elérhető...

						this.router.navigate(['/users', this.userId, 'tasks'], {
							replaceUrl: true,
						});

					A 'replaceUrl: true' azt csinálja, hogy az elnavigálás után lecseréli az url-t, azaz a 
					browser 'Vissza' gombjával nem megy vissza az elnavigált oldalra.
					Egy új task létrehozása után ez hasznos (a Create után már nem akarjuk, hogy a létrehozó oldalra vissza tudjon menni).

			Nem létező route kezelése (path:'**')

				Ha az url-be nem létező route kerül beírásra, akkor készíthető erre a célra egy komponens,
				jelezni ezt a user-nek.

				export const routes: Routes = [
					{ path: '', component: NoTaskComponent },
					{
						path: 'users/:userId',
						component: UserTasksComponent,
						children: [
							{ path: 'tasks', component: TasksComponent },
							{ path: 'tasks/new', component: NewTaskComponent }
						]
					},
					{
						path: '**',
						component: NotFoundComponent
					}
				];

			Redirecting users

				Egy megadott path-hoz hozzárendelhető egy másik path (redirectTo) egy adott component helyett.

				export const routes: Routes = [
					{ path: '', component: NoTaskComponent },
					{
						path: 'users/:userId',
						component: UserTasksComponent,
						children: [
							{
								path: '',
								redirectTo: 'tasks',
								pathMatch: 'prefix'
							},
							{ path: 'tasks', component: TasksComponent },
							{ path: 'tasks/new', component: NewTaskComponent }
						]
					},
					{
						path: '**',
						component: NotFoundComponent
					}
				];

				Pl. a példában egy user kiválasztására a következő navigáció van:
				
				 localhost:4200/users/u2/tasks  (u2 az aktuális user id)
				 	- a tasks mindig hozzáadódik, hogy a user nevén kívül a hozzá tartozó taskok is megjelenjenek.
					- Ez a child route-os megoldás, ahol a beágyazott komponens egy külön <router-outlet>-ben jelenik meg (tasks)
				
				Ha az url-be csak a localhost:4200/users/u2 kerül beírásra, akkor egy nem kívánatos UI megjelenítés lesz,
				ahol a taskok nem látszanak.

				A fenti megadásban az '' jelenti, hogy a 'tasks' helyén nincs semmi, akkor menjen a 'tasks'-os path-ra.
				Tehát a program használata során nem kerül az url-be user oldal 'tasks' végződés nélkül, de a user be tudja gépelni.
				A routes definíciókban megoldható, hogy olyankor átirányításra kerül a path.

				pathMatch: full/prefix

					- Amikor a rendszer keresi a megadott routes-ok közt, melyik felel meg a url-ben szereplő-nek.
						Tehát valami beírásra kerül az url-be a routes tömböt sorban nézi a program és az első megfelelő
						path esetén az ott megadott component (vagy egy redirected path) lesz az aktív.
						Jelen esetben a parent url + path: 'users/:userId/' + ''
							Route akkor kerül kiválasztásra, ha 
								full - url teljes egészében megegyezik
								prefix - url eleje megegyezik

			Route definíciók szeparált fájlokba rendezése

				Nem szükséges, de bevett gyakorlat nagyobb rendszerekben az áttekinthetőség kedvéért.

				Pl. az eddigi app.routes.ts-ben lévőből a child route-okat áthelyezhetjük.

				- app.routes.ts

					// Importáljuk az új fájlból, routes név itt már foglalt...
					import { routes as userRoutes} from "./app/users/users.routes"

					export const routes: Routes = [
						{ path: '', component: NoTaskComponent },
						{
							path: 'users/:userId',
							component: UserTasksComponent,
							children: userRoutes,
						},
						{
							path: '**',
							component: NotFoundComponent
						}
					];

				- users.routes.ts (új route fájl)

					import { Routes } from "@angular/router";

					import { NewTaskComponent } from "../tasks/new-task/new-task.component";
					import { TasksComponent } from "../tasks/tasks.component";

					export const routes: Routes = [
						{
							path: '',
							redirectTo: 'tasks',
							pathMatch: 'prefix'
						},
						{ path: 'tasks', component: TasksComponent },
						{ path: 'tasks/new', component: NewTaskComponent }
					];

			Routes query paraméterek

				Az url-hez íródó, ?-jel kezdődő részek kezelése. Hozzáadás és kiolvasás.
				Úgynevezett query paraméterek közvetíthetőek az url-en keresztül.

				Pl. egy link tag-be így helyezhetők el.
					queryParams direktívához lehet bind-olni
						- A komponensben ehhez kell az 'imports: [RouterLink]
						- Egy object-et vár, így property binding kell [queryParams]
					A bind-olt értékben lévő key-value párok jelennek meg az url-ben a ? után.
					Ha több értékpár van, akkor & jellel elválasztva kerülnek be.
								
				<a routerLink="./" [queryParams]="{order: 'asc'}">
					Sort Ascending / Descending
				</a>

				routerLink="./" : Speciális megadás, ami azt jelenti maradjon az aktuális oldalon.
					- Használatával a query paraméter önmagában állítható

				url: localhost:4200/users/u2/tasks?order=asc

				Paraméterek visszanyerése

					- withComponentInputBinding() konfigurációs beállítás segítségével

						Ehhez a komponensben kell egy azonos nevű input signal:
							order = input<'asc' | 'desc'>();

					- ActivatedRoute segítségével
						A paramMap-hoz hasonlóan van egy queryParams observable is.

						// Azonos nevű property
						order?: 'asc' | 'desc';
						
						ngOnInit() {
							const subscription = this.activatedRoute.queryParams.subscribe({
								next: params => this.order = params['order'],
							});

							this.destroyRef.onDestroy(() => subscription.unsubscribe());
						}

						Megjegyzés:
							Van queryParamMap observable is hasonlóan a paramMap-hoz...
			
			Statikus adatok hozzáadása a route-hoz

				- data property tartalmazhat egy object-et, amely tartalma tetszőlges.

					export const routes: Routes = [
						{ path: '', component: NoTaskComponent },
						{
							path: 'users/:userId',
							component: UserTasksComponent,
							children: userRoutes,
							data: {
								message: 'Hello!',
							},
						},
						{
							path: '**',
							component: NotFoundComponent
						}
					];

				- Az object tartalma a komponensben input signal-lal megkapható

				  message = input.required<string>();

			Dinamikus adatok hozzáadása a route-hoz

				Egy korszerű feature...

				- resolve property
					Resolver függvény megadása, ami egy külső függvény és annak visszatérési értéke tud átmenni a route-on keresztül.
					Több resolver függvény is megadható (több key-value pár az object-ben)

					export const routes: Routes = [
						{ path: '', component: NoTaskComponent },
						{
							path: 'users/:userId',
							component: UserTasksComponent,
							children: userRoutes,
							data: {
								message: 'Hello!',
							},
							resolve: {
								userName: resolveUserName,
							},
						},
						{
							path: '**',
							component: NotFoundComponent
						}
					];

				A resolver függvény egy ResolveFn típusú függvény, ami megadja többek közt milyen paraméterekkel kell rendelkeznie.
					- ActivatedRouteSnapshot: Az ActivatedRoute pillanatnyi adatai, mivel minden navigációs esemény esetén meghívódik az
						aktuális tartalommal.
					- RouterStateSnaphot - szintén pillanatnyi adatokkal
					- Generikus függvény így meg kell adni a visszatérési típust (itt most <string>)
					- Ez egy külső függvény, ami nem fér hozzá a komponensek adataihoz, de lehetőség van service-ek injektálására.
						Itt a UserService van behúzva és azon keresztül fér hozzá user adatokhoz.
					- A path paraméterben átadott userId alapján keresi meg a userService-ben a user nevet

				export const resolveUserName: ResolveFn<string> = (
					activatedRoute: ActivatedRouteSnapshot,
					routeState: RouterStateSnapshot) => {
					
					const usersService = inject(UsersService);

					const userName = usersService.users.find((u) => u.id === activatedRoute.paramMap.get('userId'))?.name || '';

					return userName;
				}

				Ezzel valójában annyi történt, hogy a korábbi komponensben lévő kód:

				  userName = computed(() => this.usersService.users.find((u) => u.id === this.userId())?.name)

					- Illetve a hozzá kapcsolódó service-ek (ActivatedRoute, UserService) kezelése is feleslegessé vált

				Helyette: Egy újabb input signal, ami a resolve object-ben nevezett key-hez bind-olódik

				  userName = input.required<string>();

				A kikerülő kódok pedig a resolver függvényben vannak megvalósítva.
				Ez a függvény külön fájlba szervezhető, így a komponens kódja még átláthatóbb lehet.
				Egy elegáns módja a data fetching kiszervezésének.
				
				Megjegyzések:
					- Mivel a userName a route-on keresztül jön ebben a megoldásban, így a komponensben szintén hozzáférhető az
						ActivatedRoute-on keresztül (nem csak input signal-lal), ahogy a többi hasonló esetben.
						- Az ActivatedRoute-ban van egy data nevű observable.
							Arra feliratkozva hozzáférhetünk a resolve-ban megadott objektumhoz (aktuális adatokkal feltöltve).

					-	Resolver függvények helyett korábban class-ok használata volt, ahol függvény helyett egy-egy osztályt kell
						definiálni és a resolve object-ben azt kell megadni.

				Resolver függvények futtatásának vezérlése

					- Az alapértelmezett viselkedés, hogy a route paraméter változása esetén fut le újra,
						a query paraméter változására nem. 
						Így a korábbi példában az asc/desc query paraméterek változásának nincs hatása a resolver függvényben.

						Ezt az alapértelmezést lehet módosítani a routes definícióban (runGuardsAndResolvers):

							path: 'tasks', // <your-domain>/users/<uid>/tasks
							component: TasksComponent,
							runGuardsAndResolvers: 'paramsOrQueryParamsChange',
							resolve: {
								userTasks: resolveUserTasks,
							},

			Title szövegek hozzáadása a route-hoz

				Title szöveg, ami a böngésző tab fülön megjelenik (az url-nél beszédesebb UI lehetőség).

				A Routes-nak van egy title property-je, ami lehet egy sima string (statikus title megadás)
				vagy lehet egy resolver függvény (dinamikus title megadás).
				A resolver függvény így tetszőleges helyekről tudja begyűjteni az aktuális title tartalmát (userName, bármi...)
		
			Route Guards

				A route-okhoz rendelhető függvény vagy régebbi esetben class, amely jogosultsági ellenőrzést végezhet.
				Szabályozva az egyes route-ok elérhetőségét.

				A route definícióban can... kezdetű property-k beállításával lehet szabályozni a hozzáférést.

				canMatch: Ez a legösszetettebb, de a legtöbbet használt property
				canActivate: A canMatch ellenőrzés után kerül ellenőrzésre
				canDeactivate: User elhagyhatja-e az adott oldalt.
				canActivateChild: Ha van children megadva, akkor annak elérhetősége eltérhet a parent-től

				A canMatch egy tömb, ami függvényeket tartalmazhat.
				
					canMatch: [dummyCanMatch],

				A függvény definíció bárhol lehet

				// Formája kötött, CanMatchFn típusú függvény, két paraméterrel.
				// A függvénybe lehet injektálni bármilyen service-t, ahogy a resolver-nél.
				// Visszatérési értéke GuardResult: boolean, RedirectCommand vagy olyan observable, amely ilyen típusokat ad.
				// 	type GuardResult = boolean | UrlTree | RedirectCommand
				const dummyCanMatch: CanMatchFn = (route, segments) => {
					
					const router = inject(Router);
					const access = Math.random();

					if (access < 0.5) {
						return true
					}
					
					// Ez jelen esetben a NotFoundComponet-re megy.
					return new RedirectCommand(router.parseUrl('/unauthorized'));
				}

				Példa: canDeactivate

					Route definícióban:
						canDeactivate: [canLeaveEditPage],

					Függvény:
						CanDeactivateFn típusú,
						Több paramétere is lehet, az első a komponens, amelyikhez tartozik a route
						Generikus függvény, a típus az átadott komponens típusa
						Visszatérési értéke GuardResult
							type GuardResult = boolean | UrlTree | RedirectCommand

						// Mivel paraméterben megkapja a komponenst, hozzáfér a public property-khez.
						export const canLeaveEditPage: CanDeactivateFn<NewTaskComponent> = (component) => {

							// Kell egy plusz ellenőrzés, hogy ha minden mező ki van töltve, akkor ne jelenjen meg a kérdés...
							// (submitted lehet egy saját property, a Submit esetén beállítva)
							if (component.submitted) {
								return true;
							}

							if (component.enteredTitle() || component.enteredDate() || component.enteredSummary()) {
									return window.confirm('Are you sure to leave this page (your data will lost)?');
							}
							return true;
						}

						A browser vissza gombjára is lefut, akkor is megjelenhet a kérdés.

			Oldal újratöltse, programból végzett navigáció konfigurálása

				A jelen példában egy task-ot a Complete gombjával lehet elvégzettre állítani.
				Ekkor kikerül a listából (törlődik).
				Viszont az eddigi beállításokkal törlődik, de nem tűnik el azonnal a listából.
				Oka, hogy a resolver függvény, ami beolvassa a task-okat nem fut le újra,
				mivel nem történik navigáció.

				Programból kell navigációs eseményt generálni, hogy ez megtörténjen.

				  // Navigációhoz kell a Router
					private router = inject(Router);
				  
					// Speckó beállításhoz ez is kell...
					private activatedRoute = inject(ActivatedRoute);

					onComplete() {
						this.tasksService.removeTask(this.task().id);

						// Az aktuális oldalra navigálás jó, de önmagában nem működik,
						// konfigurációs objektumban is kellenek beállítások...
						this.router.navigate(['./'], {
							// Kell az ActivatedRoute (mivel ez volt a tananyagban :))
							relativeTo: this.activatedRoute,
							
							// Beállítás az aktuális oldalra navigálás esetén (default az 'ignore', ami nem lenne jó).
							onSameUrlNavigation: 'reload',
						});
					}

					Ez még mindig nem elég, a router definícióban is kell beállítás:

						A 'paramsOrQueryParamsChange' helyett 'always', mivel sem a query paraméterek, sem a path paraméterek nem változnak,
						így csak always esetén hajtódik végre a resolver.

						{
							path: 'tasks', // <your-domain>/users/<uid>/tasks
							component: TasksComponent,
							runGuardsAndResolvers: 'always',
							resolve: {
								userTasks: resolveUserTasks,
							},
						},

					És mégegy dolog jelentkezik, ha van beállítva query paraméter (asc/desc - url-ben látszik)
					és ekkor van a Complete, a programból végzett navigáció után a query paraméter eltűnik.

					A queryParamsHandling beállítása szükséges

				  	this.router.navigate(['./'], {
							relativeTo: this.activatedRoute,
							onSameUrlNavigation: 'reload',
							queryParamsHandling: 'preserve',
						});

					Szóval lehet sok speciális eset, amire mindig meg kell találni melyik beállítások jók...??

Lazy Loading

	Nem töltődik le minden kód egyszerre. Lehetőség van kisebb részekre osztani az egész applikációt 
	és csak mindig a szükséges részek kerülnek letöltésre a szerverről.
	Pl. induláskor egy gyorsabb indulás érhető el.

	Kell rá mechanizmus, hogy amire viszont szükség van az lejöjjön...

	Az Angular két fő módszert biztosít

		A két módszer nem egymás alternatívája, hanem akár mindkét módszer használható vegyesen.

		- Route-based Lazy Loading
			Az applikáció egyik féle felosztása lehet a route-ok alapján való felosztás. 
			Mindig csak az aktív route komponense(i) töltődnek le a szerverről
			Ez kicsit ellentmond a single page elvnek, amikor ha nem történik adatcsere a szerverrel,
			akár semmi hálózati kommunikáció nincs a program oldalai közti mozgáskor (mivel előtte minden lejött már).
			Nagy méretű applikációk esetén ez a teljesítmény rovására mehet, ahol mégis célszerű lehet
			ez a felosztás.

			Ehhez elemezni kell a kódot, hogy mely route-ok azok, amelyeket nem szükséges induláskor letölteni.

			Lazy loading beállítása egy adott route path-ra:

				A routes definícióban a komponens meghatározását kell átalakítani úgy, hogy az adott komponenst ki lehessen venni
				az import részből.

				Pl.: Ezt kellene megszüntetni
					import { TasksComponent } from '../tasks/tasks.component';

				Az importált elemek ugyanis azonnal letöltődnek.

					component helyett loadComponent:

					{
						path: 'tasks', // <your-domain>/users/<uid>/tasks
						//component: TasksComponent,
						loadComponent: () => import('../tasks/tasks.component').then((mod) => mod.TasksComponent),
						runGuardsAndResolvers: 'always',
						resolve: {
							userTasks: resolveUserTasks,
						},
					},

					A loadComponent egy függvényt vár.
					Az függvény akkor hívódik, amikor az adott route aktiválásra kerül.
					Az ebben lévő import függvény aszinkron módon letölt egy komponenst.
					Az import függvényben azt az elérési utat kell megadni, ami a megszüntetendő importnál volt.

					A kódban minden egyéb függőséget meg kell szüntetni, ami az import-ból törlendő komponensre vonatkozik,
					ahhoz hogy törölhető legyen.

			Lazy loading egy route group-ra

				Célszerűbb lehet egy-egy route groupra beállítani a lazy loading-ot. 
				Ehhez a kódot tudatosan ilyen group-okra kell osztani.

					Az egész route import-ot kell megszüntetni (child route group)
						import { routes as userRoutes } from './users/users.routes';

					children helyett loadChildren

						{
							path: 'users/:userId', // <your-domain>/users/<uid>
							component: UserTasksComponent,
							//children: userRoutes,
							loadChildren: () => import('./users/users.routes').then((mod) => mod.routes),
							canMatch: [dummyCanMatch],
							data: {
								message: 'Hello!',
							},
							resolve: {
								userName: resolveUserName,
							},
							title: resolveTitle,
						},

					Az összes group-ban szereplő route komponense csak később kerül letöltésre,
					amikor aktiválásra kerül a children route.

					Ha jól szervezett a kód, akkor az egyéb függőségek is group-on belül vannak, így azokkal nem
					kell foglalkozni (de az mindig az adott esettől függ)...

			Service lazy loading

				Egy service default definíciója 
					@Injectable({ providedIn: 'root' })

				Ebben az esetben induláskor betöltődik.
				Ha a kód group-okba van szervezve és vannak olyan service-ek, amelyek csak az adott group-hoz
				tartoznak célszerű azokat is csak szükség esetén letölteni.

				Service definícióból törlendő a 'root' megadása
					@Injectable()

				Egyéb úton kell a service-t betölteni.
				A route group definíciójában, azzal a megoldással, hogy az egész route group-ot egy path alá kell
				sorolni children-ként (wrapper path, ami '').

				A wrapper path-hoz megadható a service a providers tömbben, így az összes children-ben elérhető lesz
				és csak akkor töltődik le, amikor a route aktiválásra kerül.

					export const routes: Routes = [
					{
						path: '',
						providers: [TasksService],
						children: [
							{
								path: '',
								redirectTo: 'tasks',
								pathMatch: 'full',
							},

		- Defarrable Views (elhalasztható)

			Egy másik lehetőség a halasztott letöltésre. Adott komponens csak akkor töltődjön le,
			amikor megjelenítésre kerül (pl. scrollozással megjelenik a böngészőben, ami addig nem látszott).

			A template-ben alkalmazott @defer blokk:

			@defer {
				<app-offer-preview />
			}

			Lehet megadni feltételeket (trigger), hogy mikor jelenjen meg, pl. a viewport biztosítja, hogy akkor
			aktiválódjon, ha a böngésző látható részére kerül a komponens.
			Ebben az esetben kötelező megadni egy @placeholder blokkot, ami akkor jelenik meg, amikor a defer
			rész nem aktív (elméletileg ezt nem fogja látni a user).

			@defer(on viewport) {
				<app-offer-preview />
			} @placeholder {
				<p>We might have an offer...</p>
			}

			Másik ilyen trigger az on interaction, ahol akkor töltődik le, amikor a user pl. rákattint.
			Addig a placeholder látszik.

			@defer(on interaction) {
				<app-offer-preview />
			} @placeholder {
				<p class="fallback">We might have an offer...</p>
			}

			Ez kiegészíthető template variable segítségével, hogy milyen interaction-re reagáljon

			<button type="button" #greeting>Hello</button>

			@defer(on interaction(greeting)) {
				<app-offer-preview />
			} @placeholder {
				<p class="fallback">We might have an offer...</p>
			}

			Prefetch

			@defer(on interaction(greeting); prefetch on hover) {

			Kattintásra kerülne letöltésre, de a hover esetén arra számítunk, hogy kattintani fog, így
			korábban elkezdi letölteni, hogy mire kattint már lent legyen...

Deploying Applications

	Ha elkészült egy alkalmazás különböző módok vannak build-elni és telepíteni őket

		- SPA (Single Page Application)
		- SSR (Server Side Rendered)
		- SSG (Static Side Generation) 

	Ezeknek megfelelően más módokon kell őket deploy-olni.

	A fejlesztés közben használt development server nem egy optimalizált kóddal működik, ott a fejlesztés segítése a cél.
	- npm start  (http://localhost:4200/)

	Telepítésre kész verziót külön kell fordítani:

	- ng build (npm run build)
	- Az eredmény a dist folder-ben jön létre
		- browser folder
		- server folder (SSR, SSG esetben)

	Single Page Application
		Egyetlen html oldal kerül letöltésre az összes javascript kóddal együtt.
		A böngészőben megjelenő minden tartalom a kliens oldalon kerül legenerálásra a javascript kódokkal.
		Az ilyen applikáció host-olásához elegendő ún. static host szolgáltatás.
		Semmilyen js kód nem kerül futtatásra a szerveren.
		Lehetséges hátrányok: 
			- Nagy alkalmazások esetén induláskor időlegesen hiányozhatnak tartalmak a megjelenítésben.
			- Kereső motorok nehezebben detektálják az oldal tartalmát (mivel a tartalom a kliens oldalon képződik)

		Deploying példa (Firebase)

			- Login with Google account
			- Create a new project, tetszőleges névvel
			- Hosting (for static websites)
				- Get started
				- npm install -g firebase-tools
				- firebase login
				- firebase init
					- Kérdések, mely firebase feature-öket szeretnénk használni
						- Hosting elegendő ebben az esetben (Configure files for Firebase Hosting...)
					- Melyik project-hez? (existing project)
					- Select the project
					- Give where is the public directory 
						- dist/router/browser
					- Kérdés: Is it a Single Page App 
						- yes
					- Firebase specifikus (konfigurációs) fájlok kerülnek hozzáadásra a projekthez
				- Build-elni kell az alkalmazást, ha még nem volt
					- ng build (npm run build) -> dist folder
				- firebase deploy
					- Deploy complete!
					- Kapunk egy elkészített weboldal linket (Hosting URL), ahonnan elérhető a telepített alkalmazás
				
			Ez eddig egy kézi telepítési eljárás.
			Létezik automatikus lehetőség is az Angular-ban.

			ng add @angular/fire
				
				Firebase deploy képesség hozzáadása a projecthez (third party package hozzáadása)
				Automatikusan beállítja a konfigban (angular.json) a deploy szekciót.

			ng deploy
				Végigviszi a deploy lépéseket, szükség esetén login, ...

	Server Side Rendered Application

		Az SPA hiányosságait próbálja orvosolni ez a lehetőség. Kacifántosabbá teszi az egészet, de bizonyára igény van rá...

		Az ilyen applikáció host-olásához szükséges egy dynamic web server (js kódok futtatására képes...).
		Firebase is támogatja, Hosting helyett az App Hosting használata szükséges.

		Ebben az esetben egy új oldal lekérése esetén a teljes oldal előállításra kerül a server oldalon,
		és egy kész oldalt kap a kliens (nincs hiányzó tartalom). Az iniciális renderelés után SPA lesz belőle (hydrated).
		Így a kezdeti esetben esetlegesen hiányzó tartalom megoldódik és a későbbi használat is gyors marad az SPA-val.

		Kereső motorok is jobban szeretik, ha egyből van tartalom.
		Viszont esetlegesen tovább tarthat mire az első oldal lejön (user várakozhat)
		
		SSR hozzáadása egy létező projekthez (ha nem így volt eredetileg létrehozva)

			ng add @angular/ssr
				Szükséges library-k hozzáadása, konfigurációs beállítások elvégzése.
			
			Létrejön egy server.ts fájl, ami a node.js server kódot tartalmazza.
			Ez a server állítja elő a dinamikusan keletkező oldalakat a weboldal lekérése esetén. 
		
		SSR esetben build esetén nem csak egy browser folder keletkezik, hanem egy server folder is a dist mappában.
	
		A lefordított app futtatása (package.json/script részben van az új bejegyzés hozzá):

			npm run serve:ssr:routing
				Egy másik porton lesz elérhető az app:
					http://localhost:4000

	Static Site Generation

		Tovább gönygyölítve a hülyeséget mivel az SSR lassabban adja vissza a kért oldalt lehet olyat csinálni, hogy
		bizonyos oldalak a build-elés folyamán előrenderelésre kerülnek.
		Ezeket egyből vissza tudja adni a server, mert ezekkel lekéréskor már nem csinál semmit.
		Viszont ezek csak olyan oldalak lehetnek, amelyeken nincs dinamikus tartalom.

		Ha egy alkalmazás csak SSG oldalakat tartalmaz, elegendő hozzá egy static host, egyéb esetben dynamic host szükséges.

		Az SSR alkalmazások esetén automatikusan prerendelésre kerülnek az erre alkalmas oldalak.
		
Authentication

	...

NgRx (A Complex State Management System)

	Egy külön package az Angular-hoz, amely bonyolult (nagy összetettségű) project-ek esetén segíthet kezelni a alkalmazásban
	szereplő állapotokat. Ezek adatok, amelyek általában befolyással vannak a UI-ra
	Minden ilyen "adat" központosított tárolását, kezelését, ... teszi lehetővé
	(ahelyett, hogy külön service-ekben, komponensekben lennének elhelyezve).


	Store
		Központi tároló objektum, ahol minden állapot (adat) tárolásra kerül.
		Ahol szükség van ezekre, oda injektálással hozzáférhető a Store.

	Selectors
		A komponensek ezeken keresztül tudják megkapni a Store-ban lévő adatokat.

	Actions
		Fejlesztő által definiált művelet típusok, amelyeket egy-egy adott adaton kell elvégezni.

	Reducers
		Adatokon végzendő konkrét műveletek
		Adatok létrehozása is ezzel lehetséges, ami a kezdeti értékadással történik.

	Effects
		Az adatokon végzett módosítások mellett szükség lehet a módosításkor egyéb műveletek elvégzésére is.
		Ezek ún. side effect-ek és nem célszerű pl. a reducer kódjában elhelyezni őket, ahol jobb ha csak az adattal
		való műveletek vannak.
		Ilyen effect-ek csatolásával lehet az ilyen műveleteket hozzáadni.
		Lehetséges ilyen effect-ek: HTTP kérés, localStorage tárolás, log üzenet a console-ra, ...

	Install NgRx (third party library)

		Meglévő project-hez való hozzáadás:		

		ng add @ngrx/store

		Standalone komponensek esetén a main.ts automatikusan módosításra kerül:

			import { provideStore } from '@ngrx/store';

			bootstrapApplication(AppComponent, {
			    providers: [provideStore()]
			});

		Ezzel létrejön a központi Store objektum.

	Reducers
	Kezdetben a Store üres természetesen. Adatok létrehozásához szükség van egy Reducer-re.
	Ami az adatokon végzendő műveleteket tartalmazza.
	Tartalmazza az adat típusát és kezdeti értékét is.
	Így ezen keresztül jön létre az adat a Store-ban.
	
	Célszerű külön fájlba elhelyezni a reducer kódját
		(itt nincs művelet hozzáadva még... csak a kiindulási érték)

		import { createReducer, on } from "@ngrx/store";

		const initialState = 0;

		export const counterReducer = createReducer(
		  initialState,
		);

	A main.ts-ben hozzáadni a Store-hoz:
	(itt kap nevet az adat (counter), típusa pedig a reducer által szolgáltatott adat típusa lesz (number))

		bootstrapApplication(AppComponent, {
		    providers: [provideStore({counter: counterReducer})]
		});

	Adatok lekérése a Store-ból
		A lekérdezett adat egy Observable, amelynek típusa az adat típusának megfelelőnel kell lenni.
		Ezért a $, ami observable-k konvencionális jelölése (nem kötelező, de jó szokás)

		count$: Observable<number>;

		constructor(private store: Store<{counter: number}>) {
			this.count$ = store.select('counter');
		}

		A konstruktorban kerül beinjektálásra a Store-t.
		A Store-ból a select-tel lehet lekérni a nevezett adatot.
		Ahhoz, hogy a select-nél az adat neve (counter) ismert legyen, szükséges a generikus Store-nál megadni a típust,
		Store<{counter: number}>

		Az Observable esetén szükséges a subscribe(), de adott esetben nem kell kézzel feliratkozni, 
		mert az async Pipe használható.

		<p class="counter">{{ count$ | async }}</p>

	Adatok módosítása (Actions)
		Reducer-ekben lesz a módosítási logika.
		Reducer-ek nem közvetlenül vannak meghívva, hanem Action-ök segítségével (dispatch Action).

	Célszerű az Actions kódot is külön fájlba helyezni

		import { createAction } from "@ngrx/store";

		export const increment = createAction(
		  '[Counter] Increment'
		);

		A createAction paraméterként alapból egy string azonosítót kap.
		Az azonosító itt kerül meghatározásra, valamilyen egyedi azonosítónak kell lennie (string).
		Konvencionális jelölés (nem kötelező), hogy a név elé írják szögletes zárójelben, hogy mely adatra vonatkozik.

		A létrejött változó (increment (Action)) az export-tal hozzáférhető máshonnan.

	A Reducer-ben kell beállítani, hogy mely Action-re reagál és azt, hogy mit (hogyan módosítja az adatot)

		export const counterReducer = createReducer(
		  initialState,
		  on(increment, (state) => state + 1)
		);

		on - @ngrx/store által szolgáltatott függvény, amivel a figyelt Action beállítható...
			Fontos, hogy a módosítást végző függvény sose módosítsa az eredeti adat példányt, hanem egy újat adjon vissza.
			Ez akkor lényeges, ha valamilyen referencia típusról van szó, pl. array esetén egy új array jöjjön létre a módosítással.
			Az eredeti adat a state paraméterben érkezik.

	Action aktiválása (dispatch)

		constructor(private store: Store) {}

		increment() {
			this.store.dispatch(increment());
		}

		Az adott komponensben a konstruktorban injektálni kell a Store-t.
		A megfelelő helyen dispatch függvény segítségével hívható az Action.
		Fontos, hogy az adott Action-t itt függvényhívásként kell megadni (increment()) !


	Paraméter csatolása Action-höz

		Az increment fix-en egyel növeli az értéket, így nem igényel paramétert.
		Ha meg akarjuk határozni mennyivel növeljen, akkor szükséges.

		Az Action definícióban adhat meg:

			import { createAction, props } from "@ngrx/store";

			export const increment = createAction(
			  '[Counter] Increment', 
			  props<{value: number}>()
			);
		
		props függvény használatával, ahol a generikus típus határozza meg a paramétert.
		'value' egy tetszőleges név és a típus, ami még fontos.

		A hívás helyén (dispatch) a szükséges paramétert meg kell adni:

		increment() {
			this.store.dispatch(increment({ value: 2 }));
		}

		Ahhoz, hogy a paraméter értéke érvényre jusson a Reducer-ben is módosítani kell a műveletet.

		export const counterReducer = createReducer(
			initialState,
			on(increment, (state, action) => state + action.value)
		);

		Az eredeti adat (state) paraméter mellé automatikusan hozzáadódik az 'action' paraméter, ami tartalmazza
		a props függvénnyel definiált value property-t.

	Selectors

		Külön selector-okra nincs minden esetben szükség.
		Lekérni egy adatot a beépített select() függvénnyel lehet.

		this.count$ = store.select('counter');

		A select függvény többféle paraméterezéssel is hívható.
		Az egyik ilyen, amikor az adat nevét (key) adjuk meg.

		Lehetséges azonban olyan megadás, amikor egy függvény a paraméter.
		A függvény formátuma:
		
			export const selectCount = (state: { counter: number }) => state.counter;

		Olyan key-t kell megadni az object típusban, ami van a store-ban (counter)
		    providers: [provideStore({counter: counterReducer})]

		Ez a függvény használható a select-ben

			this.count$ = store.select(selectCount);

		Ez a függvényes megoldás akkor lehet jó, ha pl. befolyásolni szeretnénk a visszakapott adatot még a beolvasásnál:

			export const selectDoubleCount = (state: { counter: number }) => state.counter * 2;

		Tekinthetjük a központosítás még egy fokának, mivel ha az adott függvény sok helyről kerül felhasználásra, 
		és változik valami a tárolásban, akkor csak a függvényben kell módosítani (jó esetben...).

		Több selector függvény fűzhető össze a createSelector-ral, így a fenti eset megadható így is:

			export const selectDoubleCount = createSelector(
				selectCount,
				(state) => state * 2
			);

	Effects
		Használatához szükséges plusz package hozzáadása

		ng add @ngrx/effects
		
		Standalone komponensek esetén a main.ts automatikusan módosításra kerül:

			import { provideEffects } from '@ngrx/effects';

			bootstrapApplication(AppComponent, {
			    providers: [provideStore({ counter: counterReducer }), provideEffects()]
			});

		Célszerű külön fájlba rendeznia az effect-eket

		import { Actions, createEffect, ofType } from "@ngrx/effects";
		import { tap } from "rxjs";
		import { decrement, increment } from "./counter.actions";

		export class CounterEffects {
			saveCount = createEffect(() =>
				this.actions$.pipe(
					ofType(increment, decrement),
					tap((action) => {
						console.log(action)
						localStorage.setItem('count', action.value.toString());
					})
				), { dispatch: false }
			);

			constructor(private actions$: Actions) { }
		}

		Egy osztály, amelynek property-jeit tekinthetjük egy-egy pipeline-nak, amelyek egy-egy effect megvalósításai (saveCount).
		Az action által érintett adatot írja a localStorage-be.

		A konstruktorban kell beinjektálni az Actions observable-t.
		Ez kiváltódik minden alkalommal, amikor az alkalmazásban bárhol valamilyen action kerül meghívásra (dispatch).
		
		createEffect függvénnyel hozható létre egy effect (pipeline), amelynek paramétere egy függvény, 
		ami az observable láncot írja le az adott esetre.

		ofType() - szűrő, amivel kiválasztható melyik action(ök)-re legyen hatással az effect.

		tap() - Valamilyen művelet hajtható végre vele, ahol az action értéke paraméterként megvan.

		{ dispatch: false } - Amennyiben az effect-ben végrehajtott művelet nem hív egyéb action-t (dispatch), akkor ez beállítandó.

	Store-ban tárolt adatok elérése effect-ekből
		
		A fenti példában az action az increment vagy decrement értéket tartalmazza, a konkrét counter érték nincs itt.
		Így a tárolt adat sem a counter.

		Ehhez be kell olvasni a Store-ból a counter-t.

		export class CounterEffects {
			saveCount = createEffect(() =>
				this.actions$.pipe(
					ofType(increment, decrement),
					withLatestFrom(this.store$.select(selectCount)),
					tap(([action, counter]) => {
						console.log(action)
						localStorage.setItem('count', counter.toString());
					})
				), { dispatch: false }
			);

			constructor(
				private actions$: Actions,
				private store$: Store<{ counter: number }>
			) { }
		} 

		Konstruktorban be kell húzni a Store-t.

		withLatestFrom - rxjs operator, amellyel egyéb observable-k húzhatók be az adott pipeline-ba.
			- Jelen esetben a select-tel kiválasztott adat observable-je lesz az
			- A továbbiakban a pipeline paramétere megváltozik (az eddigi action helyett)
				- Egy tömb lesz, amely tartalmazza az action-t és a beemelt observable-ket.
				- [action, counter] // ez esetben a lekérdezett store adat (counter) observabe-t



NEWS

	Angular DevTools
		Böngésző (Chrome) kiegészító debug-olni, elemezni egy Angular alkalmazást...
		Külön kell telepíteni a Chrome-hoz.
		https://chromewebstore.google.com/detail/angular-devtools/ienfalfjdbdpebioblfackkekamfmbnh


	GoogleFonts Lesson 16

	assets beállítás amgular.json-ben

