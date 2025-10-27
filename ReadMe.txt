
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


NEWS

	Angular DevTools
		Böngésző (Chrome) kiegészító debug-olni, elemezni egy Angular alkalmazást...
		Külön kell telepíteni a Chrome-hoz.
		https://chromewebstore.google.com/detail/angular-devtools/ienfalfjdbdpebioblfackkekamfmbnh


	GoogleFonts Lesson 16

	assets beállítás amgular.json-ben

