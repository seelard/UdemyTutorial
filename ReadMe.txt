
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
		@Component({ standalone: true})

	Angular 19-től az alapértelmezés a true, így a bejegyzés elmaradhat

	Standalone esetben nincs module a rendszerben,
	egy root komponensen (alap esetben az AppComponent) belül épül fel a rendszer akárhány komponensből (component tree).

Property binding

	HTML template-ben ts-ből jövő változók beemelése

	<img [src]="someSrc"> 
		- Ez a DOM element (HTMLImageElement) egy létező property-je (src).

	Lehet olyan eset, ahol egy HTML elem attributumához kell bind-olni

	<td [attr.colspan]="columnSpan"></td>
		- A colspan egy attribútum (ilyen névvel nincs property), a bind-hoz kell az attr. előtag.

	https://jakearchibald.com/2024/attributes-vs-properties/

Change detection mechanisms

	zone.js
		Hagyományos megoldások, háttérben működő mechanizmusok minden UI-t érintő változás lekövetésére

	signals	
		signal-ok alkalmazása (nincs szükség a zone.js-re)
		Minden signal maga gondoskodik a hozzá tartozó UI aktualizálásáról

Signal inputs
	Lesson 32

Forms
	Egy Form-on belüli submit button viselkedése alapesetben kötött.
	Adatot küldene a server felé.
	Angular esetén a FormsModule importálásával ez a viselkedés felülíródik.
	Az ngSubmit alkalmazásával saját metódus hívható a submit esetén.

	Lesson 54	

input within a Form (Two-way binding)
	ngModel használatához az imput elem-nek lennie kel name attribútumának, ez célszerűen lehet ugyanaz, mint az id

Content Projection with ng-Content

	Lesson 56
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

	Attribute selector (ez a jelen esetben javasolt megoldás)
		selector: '[appButton]' - minden helyre beillesztendő, ahol az appButton attribútum szerepel
		kombinálható
		selector: 'button[appButton]' - minden button esetén beillesztendő, ahol az appButton attribútum szerepel

		<button appButton></button>

		az 'appButton' egy tetszőleges azonosító

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

	Ha szeretnénk, hogy a span elem megjelenjen a komponens template-ben:

	<span>
		<ng-content/>
	</span>
	<span>
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
		A @ContentChild és tsi által behúzott HTML elemek onnantól elérhetőak, amikor ez meghívódik,
		tehát pl. az ngOnInit-ből még nem lesznek azok (valószínű még az ngAfterViewInit-ből sem).

	ngAfterContentChecked
		Akkor hívódik, amikor minden projektált tartalmat már ellenőrzött az Angular change detection mechanizmusa.

	ngOnDestroy
		Az adott komponens láthatóságának kikapcsolásakor, amikor kikerül a renderelendő kompoinensek közül.
		Pl. feltételes renderelés van a template-ben @if

	afterRender (afterEveryRender >=v20), afterNextRender
		Angular 16-tó elérhetőek, v20-tól afterRender -> afterEveryRender-re átnevezve

		Nem a többi life cycle hook metóduson keresztül érhetőek el (ngOnInit,...), hanem a kontruktorban.

		constructor() {
			// A regisztrált callback minden UI változáskor meghívódik (amikor újra kell renderelni valamit)
			afterRender(() => {
				console.log('EVERY');
			});

			// A regisztrált callback a következő UI változáskor meghívódik egyszer (amikor újra kell renderelni valamit)
			afterNextRender(() => {
				console.log('NEXT');
			});
		}

		NEM CSAK AZ ADOTT KOMPONENSRE ÉRTENDŐ, HANEM AZ EGÉSZ WEB SITE-RA (ANGULAR APPLICATION-ra)!

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

	#titleInput a teplate variable, amelynek #-el kell kezdődnie
	A felhasználás helyén már nem kell a #: "onSubmit(titleInput)"

	Itt egy konkrét HTMLElement kerül átadásra.

	https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement

	Jelen esetben HTMLInputElement, amelynek "value" property-je tartalmaza a beírt szöveget:

	onSubmit(titleElement: HTMLInputElement) {
    console.log(titleElement.value);
  }

	Az átadott paraméter a template-ben lehet egyből a value
		"onSubmit(titleInput.value)"
	Ekkor az egy string
		onSubmit(title: string) {
			console.log(titleElement);
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
	- Az onCleanup meghívásra kerül, ha az effect megszűnik (destroyed), ami pl. a komponens megszünésekor történik.

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
		A komponensen belüli input mezőkhöz ngModel-es two-way binding van
		A rect komponens felé van a custom two-way binding, itt is a kettős zárójel használatos [(size)]

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
	Ehhez természetesen sok háttérműködés kell,	hogy csinálja a dolgát.
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

	selector:	Úgy használatos, mint a @Component-nél,
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
		- ViewContainerRef: A hely ahová ahol a template elemek megjelennek (vagy nem)

		Ezek használhatóak a DOM megjelenítés vezérlésére:

    effect(() => {
      if (this.authService.activePermission() === this.userType()) {
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



NEWS

	Angular DevTools
		Böngésző (Chrome) kiegészító debug-olni, elemezni egy Angular alkalmazást...
		Külön kell telepíteni a Chrome-hoz.
		https://chromewebstore.google.com/detail/angular-devtools/ienfalfjdbdpebioblfackkekamfmbnh


	GoogleFonts Lesson 16

	assets beállítás amgular.json-ben

