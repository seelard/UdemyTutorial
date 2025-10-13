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


VS Code extensions (for Angular)

	Angular Language Service
		Fejlesztés közbeni Intellisense megoldások...

	Angular Essentials (John Papa)
	
	Auto Rename Tag
		HTML tag záró elemének automatikus állítása.

	Prettier


NEWS

	GoogleFonts Lesson 16

	assets beállítás amgular.json-ben

	Lesson 22 string interpolation and property binding

	Atribute binding!!  [attr.aria-valuenow]="currentValue"
	https://jakearchibald.com/2024/attributes-vs-properties/

	class getter in property binding

  Lesson 25 event binding

	Lesson 28 Signals

		computed(() => ... )

	Lesson 30, 31 Imputs
		Required, optional imputs