# Plus ou Moins

Entrons dans le vif du sujet en travaillant ensemble sur un projet concret.
Ce chapitre présente quelques concepts couramment utilisés en Rust en les
mettant en place dans un véritable programme. Nous verrons par exemple les
mots clés `let` et `match`, les méthodes et fonctions associées, ainsi que
l'utilisation de librairies externes (les `crates`) et bien plus encore.
Au cours de ce chapitre, nous verrons les bases de ces concepts à l'aide
d'exemples concrets, et les chapitres qui suivent les présenteront en détails.

Nous allons créer un programme fréquemment réalisé par les débutants en
programmation, le jeu du "Plus ou Moins". Le principe de ce jeu est le suivant:
le programme va générer un nombre aléatoire entre 1 et 100. Ce sera ensuite au
joueur d'entrer un nombre qu'il aura deviné, et le programme indiquera si le
nombre fourni par le joueur est trop grand ou trop petit. Si le nombre deviné
par le joueur est le bon, le programme affiche un message de félicitations
et se termine.


## Mise en place du projet

Pour créer un nouveau projet, rendez-vous dans le dossier *projets* que
vous avez créé au chapitre 1, et utilisez Cargo pour initialiser votre projet.

```text
$ cargo new deviner_nombre --bin
$ cd deviner_nombre
```

La première commande, `cargo new`, requiert le nom de notre projet (`deviner_nombre`)
en tant que premier argument. L'option `--bin` informe Cargo que notre projet
formera un programme exécutable, comme celui du chapitre 1, et non une librairie.
La seconde commande nous place dans le dossier de notre projet nouvellement
crée par Cargo.

Regardons le fichier *Cargo.toml* automatiquement crée:

<span class="filename">Fichier: Cargo.toml</span>

```toml
[package]
name = "deviner_nombre"
version = "0.1.0"
authors = ["Votre Nom <vous@exemple.com>"]

[dependencies]
```

Si le nom d'auteur que Cargo a obtenu depuis votre environnement n'est pas
correct, vous pouvez le changer dans ce fichier et le sauvegarder.

Tel qu'expérimenté dans le chapitre 1, `cargo new` génère un programme de base,
un “Hello, world!” (que l'on pourrait traduire par “Bonjour tout le monde”).

Jetez un oeil au fichier *src/mais.rs*:

<span class="filename">Fichier: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Maintenant, lançons la compilation de ce programme “Hello, world!”, et
démarrons-le. Il est possible de faire ces deux actions en une seule commande,
`cargo run`:

```text
$ cargo run
   Compiling deviner_nombre v0.1.0 (file:///projects/deviner_nombre)
     Running `target/debug/deviner_nombre`
Hello, world!
```

Cette commande `run` est très pratique lorsque vous souhaitez itérer rapidement
sur un projet, et c'est le cas ici: on veut lancer notre jeu, le tester,
faire une modification, et passer à l'essai suivant.

Ouvrez à nouveau le fichier *src/main.rs*. C'est ici que nous écrirons la totalité
de notre code Rust.


## Traitement des entrées utilisateur

La première partie du programme consiste à demander au joueur de fournir un nombre,
et à vérifier que ce que l'utilisateur entre correspond au format attendu.
Commençons par permettre au joueur d'entrer le nombre qu'il a deviné.
Ajoutez le code de la Figure 2-1 dans le fichier *src/main.rs*.

<span class="filename">Fichier: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Jeu de Plus ou Moins");

    println!("Entrez votre déduction.");

    let mut deduction = String::new();

    io::stdin().read_line(&mut deduction)
        .expect("Echec de la lecture de l'entrée utilisateur");

    println!("Votre déduction: {}", deducation);
}
```

<span class="caption">Figure 2-1: Code permettant de lire une entrée utilisateur et de l'afficher</span>

Ce code contient beaucoup d'information, nous allons donc le détailler petit
à petit. Pour obtenir l'entrée utilisateur et l'afficher, nous avons besoin d'importer
la librairie `io` (pour input/output, entrée/sortie) afin de pouvoir l'utiliser.
La librairie `io` provient de `std`, la librairie standard du langage Rust.

```rust,ignore
use std::io;
```

Par défaut, Rust n'importe que quelques types utilisables dans les programmes,
ceux qui sont listés dans le [le *prelude*][prelude]<!-- ignore -->. Si vous
voulez utiliser un type qui ne s'y trouve pas, vous devrez l'importer au moyen
du mot clé `use`. Lorsque l'on importe `std::io`, on rend disponible de nombreuses
fonctionnalités du domaine des entrées/sorties, comme par exemple la possibilité
de traiter les entrées de l'utilisateur.

[prelude]: ../../std/prelude/index.html

Tel qu'expliqué au Chapitre 1, la fonction `main` est le point d'entrée
dans le programme, c'est ici que l'exécution démarre.

```rust,ignore
fn main() {
```

Le mot clé `fn` déclare une nouvelle fonction, les `()` indiquent que cette
fonction n'accepte aucun paramètre, et `{` marque le début du corps de la fonction.

Vous avez également vu lors de votre lecture du Chapitre 1 que `println!` est une
macro qui affiche une chaine de caractères à l'écran.

```rust,ignore
println!("Jeu de Plus ou Moins");

println!("Entrez votre déduction.");
```

Ce code permet simplement d'afficher le titre de notre jeu, et de demander
au joueur d'entrer un nombre.

### Enregistrer des données grace aux variables

Créons maintenant un emplacement pour enregistrer l'entrée utilisateur:

```rust,ignore
let mut deduction = String::new();
```

Le programme commence à devenir intéressant ! Il se passe beaucoup de choses
lorsque cette simple ligne est exécutée. Vous remarquerez qu'elle commence par
le mot clé `let`, qui sert à créer des *variables*. En voici un autre exemple:

```rust,ignore
let foo = bar;
```

Cette ligne permet de créer une nouvelle variable nommée `foo` et à lui
assigner la valeur de `bar`. Par défaut en Rust, les variables sont immuables,
c'est-à-dire qu'il est impossible de modifier leur valeur. Le prochain exemple
montre comment utiliser le mot clé `mut` pour autoriser la modification de la
valeur d'une variable.

```rust
let foo = 5; // immuable
let mut bar = 5; // modifiable
```

> Note: Les `//` permettent de commencer un commentaire s'étendant jusqu'à la fin
> de la ligne. Rust ignore le texte se trouvant dans un commentaire.

Vous comprenez donc maintenant que la ligne `let mut deduction` permet de créer une
variable modifiable nommée `deduction`. De l'autre côté du signe égal (`=`) se trouve
la valeur de cette variable. Ici il s'agit du retour de l'appel de la fonction
`String::new`, qui renvoie une nouvelle instance d'un `String`.
[`String`][string]<!-- ignore --> est un type qui représente une chaine de caractères,
et qui nous est fournit par la librairie standard. Les `String` représentent du texte
encodé en UTF-8, et il est possible de les étendre.

[string]: ../../std/string/struct.String.html

Les `::` de qui précèdent `::new` indiquent que la fonction `new` est une *fonction
associée* du type `String`. Une fonction associée est implémentée sur un type, dans
ce cas `String` plutot que sur une instance particulière de `String`. Ce concept
est parfois appelé une *méthode statique*.

Cette fonction `new` crée une nouvelle chaine de caractères vide, un nouveau `String`.
Vous trouverez fréquemment une fonction `new` sur les types, c'est un nom souvent
donné à une fonction qui crée une nouvelle valeur ou instance.

Pour résumer, la ligne `let mut deduction = String::new();` crée une nouvelle
variable modifiable nommée `deduction` qui contient une nouvelle chaine de caractères,
un `String`. Ouf !

Rappellez-vous que qu'on a importé les fonctionnalités d'entrée/sortie de la
librairie standard, au moyen de la ligne `use std::io;`, dès la première ligne
de notre programme. Nous allons maintenant appeler la fonction `stdin`, associée
au module `io`:

```rust,ignore
io::stdin().read_line(&mut deduction)
    .expect("Echec de la lecture de l'entrée utilisateur");
```

Si la ligne `use std::io` n'était pas présente au début du programme, on aurait
dû écrire l'appel à la fonction de cette manière: `std::io::stdin`. La fonction
`stdin` retourne une instance de [`std::io::Stdin`][iostdin]<!-- ignore -->, qui
est un type qui représente une référence vers l'entrée standard de notre programme,
liée au terminal dans lequel il est lancé.

<!-- TODO: Vérifier traduction de 'handle' en 'référence' -->

[iostdin]: ../../std/io/struct.Stdin.html

La ligne suivante, `.read_line(&mut deduction)` appelle la méthode
[`read_line`][read_line]<!-- ignore --> de notre référence vers l'entrée standard,
ce qui permet d'obtenir ce que l'utilisateur a entré au clavier. On passe à la
méthode `read_line` un argument, `&mut deduction`.

[read_line]: ../../std/io/struct.Stdin.html#method.read_line

L'objectif de `read_line` est de prendre ce que l'utilisateur écrit dans
l'entrée standard, et de le placer dans une chaine de caractères, c'est pourquoi
on passe à la méthode cet argument. Ce dernier se doit d'être modifiable, puisque
`read_line` va le modifier afin d'y placer l'entrée de l'utilisateur.

Le signe `&` indique que cet argument est une *référence*, ce qui permet de laisser
plusieurs morceaux de code accéder à un même bloc de données, sans avoir besoin
de copier ces données plusieurs fois. Les références sont une fonctionnalité
complexe, et un des avantages majeurs de Rust est qu'il rend simple et sécurisé
l'utilisation des références. Il est inutile de détailler plus que nécessaire
les références pour finir ce programme. Le chapitre 4 les présentera en détails.
Pour l'instant, tout ce que vous devez savoir est que comme les variables,
les références sont immuables par défaut, il est donc nécessaire d'écrire
`&mut deduction` plutot que `&deduction` pour rendre la référence modifiable.

Nous n'avons pas tout à fait finit de détailler ce bout de code. C'est une ligne
de texte, mais ce n'est que la première partie de la ligne de code complète.
Voici la suite:

```rust,ignore
.expect("Echec de la lecture de l'entrée utilisateur");
```

Lorsque l'on appelle une méthode avec la syntaxe `.foo()`, il est généralement
préférable de passer une ligne puis d'indenter à l'aide de caractères espace
avant d'écrire la suite du code. Ceci permet d'aider à séparer les longues lignes
de code. On aurait pu écrire le même code de cette manière:

```rust,ignore
io::stdin().read_line(&mut deduction).expect("Echec de la lecture de l'entrée utilisateur");
```

Cependant, une longue ligne de code n'est pas toujours facile à lire, il est donc
considéré comme une bonne pratique de la diviser, deux lignes de texte pour deux
appels de méthodes. Voyons maintenant l'effet de l'appel à `expect`.

### Gérer les potentielles erreurs avec `Result`

Tel qu'expliqué plus haut, `read_line` permet de stocker ce que l'utilisateur a
écrit au clavier dans la variable que l'on spécifie, mais cette fonction retourne
également une valeur, de type [`io::Result`][ioresult]<!-- ignore -->. Il existe
plusieurs types nommés `Result` dans la librairie standard de Rust: un type
générique [`Result`][result]<!-- ignore -->, ainsi que des versions spécifiques
à un sous-module, comme `io::Result`.

[ioresult]: ../../std/io/type.Result.html
[result]: ../../std/result/enum.Result.html

Les types `Result` sont des [*énumerations*][enums]<!-- ignore -->, aussi
appelées *enums*. Une énumération est un type qui peut avoir un certain nombre
de valeurs définies par le programmeur. Les différentes valeurs possibles d'un
*enum* sont appelées *variantes*. Le chapitre 6 explorera les énumération plus
en détails.

[enums]: ch06-00-enums.html

Pour `Result`, il existe deux variantes: `Ok` et `Err`. `Ok` indique qu'une
opération est un succès, et à l'intérieur de la variante `Ok` se trouve le
résultat attendu. A l'inverse, la variante `Err` de `Result` signifie que
l'opération a échouée, et à l'intérieur se trouve les informations décrivant
les raisons de l'échec.

L'objectif du type `Result` est d'encoder les informations nécessaires à une
bonne gestion des erreurs. Les types `Result`, comme tous les types, ont des
méthodes associées. Par exemple, `io::Result` propose
[la méthode `expect`][expect]<!-- ignore -->. Si une instance de `io::Result` est
la variante `Err`, un appel à `expect` sur l'instance de `io::Result` causera
un crash du programme, et affichera le message que vous aurez passé en argument.
Si l'appel à `read_line` retourne une variante `Err`, ce sera probablement
dû à une erreur du système d'exploitation. Si par contre `read_line` retourne
une variante `Ok`, `expect` prendra le contenu du `Ok`, le résultat de l'operation,
et vous le retournera afin que vous puissiez l'utiliser. Dans notre exemple,
ce résultat est le nombre d'octets que l'utilisateur a fournit dans l'entrée
standard.

[expect]: ../../std/result/enum.Result.html#method.expect

Si l'on appelle pas `expect`, le programme compilera, mais avec une mise en garde:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
src/main.rs:10:5: 10:39 warning: unused result which must be used,
#[warn(unused_must_use)] on by default
src/main.rs:10     io::stdin().read_line(&mut guess);
                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

Rust nous informe que l'on ne fait rien du `Result` qui nous fournir `read_line`,
et que par conséquent nous n'avons pas géré une erreur potentielle. La meilleure
façon de régler ce programme est d'écrire le code permettant de bien gérer
l'erreur, mais dans notre cas on souhaite seulement crasher le programme si un
problème survient, on peut donc se contenter d'appeler `expect`. Nous verrons
dans le chapitre 9 comment gérer les erreurs proprement.

### Printing Values with `println!` Placeholders

Aside from the closing curly brace, there’s only one more line to discuss in
the code added so far, which is the following:

```rust,ignore
println!("You guessed: {}", guess);
```

This line prints out the string we saved the user’s input in. The set of `{}`
is a placeholder that holds a value in place. You can print more than one value
using `{}`: the first set of `{}` holds the first value listed after the format
string, the second set holds the second value, and so on. Printing out multiple
values in one call to `println!` would look like this:

```rust
let x = 5;
let y = 10;

println!("x = {} and y = {}", x, y);
```

This code would print out `x = 5 and y = 10`.

### Testing the First Part

Let’s test the first part of the guessing game. You can run it using `cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

At this point, the first part of the game is done: we’re getting input from the
keyboard and then printing it.

## Generating a Secret Number

Next, we need to generate a secret number that the user will try to guess. The
secret number should be different every time so the game is fun to play more
than once. Let’s use a random number between 1 and 100 so the game isn’t too
difficult. Rust doesn’t yet include random number functionality in its standard
library. However, the Rust team does provide a [`rand` crate][randcrate].

[randcrate]: https://crates.io/crates/rand

### Using a Crate to Get More Functionality

Remember that a *crate* is a package of Rust code. The project we’ve been
building is a *binary crate*, which is an executable. The `rand` crate is a
*library crate*, which contains code intended to be used in other programs.

Cargo’s use of external crates is where it really shines. Before we can write
code that uses `rand`, we need to modify the *Cargo.toml* file to include the
`rand` crate as a dependency. Open that file now and add the following line to
the bottom beneath the `[dependencies]` section header that Cargo created for
you:

<span class="filename">Filename: Cargo.toml</span>

```toml
[dependencies]

rand = "0.3.14"
```

In the *Cargo.toml* file, everything that follows a header is part of a section
that continues until another section starts. The `[dependencies]` section is
where you tell Cargo which external crates your project depends on and which
versions of those crates you require. In this case, we’ll specify the `rand`
crate with the semantic version specifier `0.3.14`. Cargo understands [Semantic
Versioning][semver]<!-- ignore --> (sometimes called *SemVer*), which is a
standard for writing version numbers. The number `0.3.14` is actually shorthand
for `^0.3.14`, which means “any version that has a public API compatible with
version 0.3.14.”

[semver]: http://semver.org

Now, without changing any of the code, let’s build the project, as shown in
Listing 2-2:

```text
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
```

<span class="caption">Listing 2-2: The output from running `cargo build` after
adding the rand crate as a dependency</span>

You may see different version numbers (but they will all be compatible with
the code, thanks to SemVer!), and the lines may be in a different order.

Now that we have an external dependency, Cargo fetches the latest versions of
everything from the *registry*, which is a copy of data from
[Crates.io][cratesio]. Crates.io is where people in the Rust ecosystem post
their open source Rust projects for others to use.

[cratesio]: https://crates.io

After updating the registry, Cargo checks the `[dependencies]` section and
downloads any you don’t have yet. In this case, although we only listed `rand`
as a dependency, Cargo also grabbed a copy of `libc`, because `rand` depends on
`libc` to work. After downloading them, Rust compiles them and then compiles
the project with the dependencies available.

If you immediately run `cargo build` again without making any changes, you won’t
get any output. Cargo knows it has already downloaded and compiled the
dependencies, and you haven't changed anything about them in your *Cargo.toml*
file. Cargo also knows that you haven't changed anything about your code, so it
doesn't recompile that either. With nothing to do, it simply exits. If you open
up the *src/main.rs* file, make a trivial change, then save it and build again,
you’ll only see one line of output:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
```

This line shows Cargo only updates the build with your tiny change to the
*src/main.rs* file. Your dependencies haven't changed, so Cargo knows it can
reuse what it has already downloaded and compiled for those. It just rebuilds
your part of the code.

#### The *Cargo.lock* File Ensures Reproducible Builds

Cargo has a mechanism that ensures you can rebuild the same artifact every time
you or anyone else builds your code: Cargo will use only the versions of the
dependencies you specified until you indicate otherwise. For example, what
happens if next week version `v0.3.15` of the `rand` crate comes out and
contains an important bug fix but also contains a regression that will break
your code?

The answer to this problem is the *Cargo.lock* file, which was created the
first time you ran `cargo build` and is now in your *guessing_game* directory.
When you build a project for the first time, Cargo figures out all the
versions of the dependencies that fit the criteria and then writes them to
the *Cargo.lock* file. When you build your project in the future, Cargo will
see that the *Cargo.lock* file exists and use the versions specified there
rather than doing all the work of figuring out versions again. This lets you
have a reproducible build automatically. In other words, your project will
remain at `0.3.14` until you explicitly upgrade, thanks to the *Cargo.lock*
file.

#### Updating a Crate to Get a New Version

When you *do* want to update a crate, Cargo provides another command, `update`,
which will:

1. Ignore the *Cargo.lock* file and figure out all the latest versions that fit
your specifications in *Cargo.toml*.
1. If that works, Cargo will write those versions to the *Cargo.lock* file.

But by default, Cargo will only look for versions larger than `0.3.0` and
smaller than `0.4.0`. If the `rand` crate has released two new versions,
`0.3.15` and `0.4.0`, you would see the following if you ran `cargo update`:

```text
$ cargo update
    Updating registry `https://github.com/rust-lang/crates.io-index`
    Updating rand v0.3.14 -> v0.3.15
```

At this point, you would also notice a change in your *Cargo.lock* file noting
that the version of the `rand` crate you are now using is `0.3.15`.

If you wanted to use `rand` version `0.4.0` or any version in the `0.4.x`
series, you’d have to update the *Cargo.toml* file to look like this instead:

```toml
[dependencies]

rand = "0.4.0"
```

The next time you run `cargo build`, Cargo will update the registry of crates
available and reevaluate your `rand` requirements according to the new version
you specified.

There’s a lot more to say about [Cargo][doccargo]<!-- ignore --> and [its
ecosystem][doccratesio]<!-- ignore --> that Chapter 14 will discuss, but for
now, that’s all you need to know. Cargo makes it very easy to reuse libraries,
so Rustaceans are able to write smaller projects that are assembled from a
number of packages.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

### Generating a Random Number

Let’s start *using* `rand`. The next step is to update *src/main.rs*, as shown
in Listing 2-3:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listing 2-3: Code changes needed in order to generate a
random number</span>

We’re adding a `extern crate rand;` line to the top that lets Rust know we’ll be
using that external dependency. This also does the equivalent of calling `use
rand`, so now we can call anything in the `rand` crate by prefixing it with
`rand::`.

Next, we’re adding another `use` line: `use rand::Rng`. `Rng` is a trait that
defines methods that random number generators implement, and this trait must be
in scope for us to use those methods. Chapter 10 will cover traits in detail.

Also, we’re adding two more lines in the middle. The `rand::thread_rng` function
will give us the particular random number generator that we’re going to use:
one that is local to the current thread of execution and seeded by the
operating system. Next, we call the `gen_range` method on the random number
generator. This method is defined by the `Rng` trait that we brought into
scope with the `use rand::Rng` statement. The `gen_range` method takes two
numbers as arguments and generates a random number between them. It’s inclusive
on the lower bound but exclusive on the upper bound, so we need to specify `1`
and `101` to request a number between 1 and 100.

Knowing which traits to use and which functions and methods to call from a
crate isn’t something that you’ll just *know*. Instructions for using a crate
are in each crate’s documentation. Another neat feature of Cargo is that you
can run the `cargo doc --open` command that will build documentation provided
by all of your dependencies locally and open it in your browser. If you’re
interested in other functionality in the `rand` crate, for example, run `cargo
doc --open` and click `rand` in the sidebar on the left.

The second line that we added to the code prints the secret number. This is
useful while we’re developing the program to be able to test it, but we’ll
delete it from the final version. It’s not much of a game if the program prints
the answer as soon as it starts!

Try running the program a few times:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

You should get different random numbers, and they should all be numbers between
1 and 100. Great job!

## Comparing the Guess to the Secret Number

Now that we have user input and a random number, we can compare them. That
step is shown in Listing 2-4:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

<span class="caption">Listing 2-4: Handling the possible return values of
comparing two numbers</span>

The first new bit here is another `use`, bringing a type called
`std::cmp::Ordering` into scope from the standard library. `Ordering` is
another enum, like `Result`, but the variants for `Ordering` are `Less`,
`Greater`, and `Equal`. These are the three outcomes that are possible when you
compare two values.

Then we add five new lines at the bottom that use the `Ordering` type:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less    => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal   => println!("You win!"),
}
```

The `cmp` method compares two values and can be called on anything that can be
compared. It takes a reference to whatever you want to compare with: here it’s
comparing the `guess` to the `secret_number`. `cmp` returns a variant of the
`Ordering` enum we brought into scope with the `use` statement. We use a
[`match`][match]<!-- ignore --> expression to decide what to do next based on
which variant of `Ordering` was returned from the call to `cmp` with the values
in `guess` and `secret_number`.

[match]: ch06-02-match.html

A `match` expression is made up of *arms*. An arm consists of a *pattern* and
the code that should be run if the value given to the beginning of the `match`
expression fits that arm’s pattern. Rust takes the value given to `match` and
looks through each arm’s pattern in turn. The `match` construct and patterns
are powerful features in Rust that let you express a variety of situations your
code might encounter and helps ensure that you handle them all. These features
will be covered in detail in Chapter 6 and Chapter 18, respectively.

Let’s walk through an example of what would happen with the `match` expression
used here. Say that the user has guessed 50, and the randomly generated secret
number this time is 38. When the code compares 50 to 38, the `cmp` method will
return `Ordering::Greater`, because 50 is greater than 38. `Ordering::Greater`
is the value that the `match` expression gets. It looks at the first arm’s
pattern, `Ordering::Less`, but the value `Ordering::Greater` does not match
`Ordering::Less`, so it ignores the code in that arm and moves to the next arm.
The next arm’s pattern, `Ordering::Greater`, *does* match
`Ordering::Greater`! The associated code in that arm will execute and print
`Too big!` to the screen. The `match` expression ends because it has no need to
look at the last arm in this particular scenario.

However, the code in Listing 2-4 won’t compile yet. Let’s try it:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `std::string::String`, found integral variable
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `guessing_game`.
```

The core of the error states that there are *mismatched types*. Rust has a
strong, static type system. However, it also has type inference. When we wrote
`let guess = String::new()`, Rust was able to infer that `guess` should be a
`String` and didn’t make us write the type. The `secret_number`, on the other
hand, is a number type. A few number types can have a value between 1 and 100:
`i32`, a 32-bit number; `u32`, an unsigned 32-bit number; `i64`, a 64-bit
number; as well as others. Rust defaults to an `i32`, which is the type of
`secret_number` unless we add type information elsewhere that would cause Rust
to infer a different numerical type. The reason for the error is that Rust will
not compare a string and a number type.

Ultimately, we want to convert the `String` the program reads as input into a
real number type so we can compare it to the guess numerically. We can do
that by adding the following two lines to the `main` function body:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less    => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal   => println!("You win!"),
    }
}
```

The two new lines are:

```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```

We create a variable named `guess`. But wait, doesn’t the program
already have a variable named `guess`? It does, but Rust allows us to
*shadow* the previous value of `guess` with a new one. This feature is often
used in similar situations in which you want to convert a value from one type
to another type. Shadowing lets us reuse the `guess` variable name rather than
forcing us to create two unique variables, like `guess_str` and `guess` for
example. (Chapter 3 covers shadowing in more detail.)

We bind `guess` to the expression `guess.trim().parse()`. The `guess` in the
expression refers to the original `guess` that was a `String` with the input in
it. The `trim` method on a `String` instance will eliminate any whitespace at
the beginning and end. `u32` can only contain numerical characters, but the
user must press the Return key to satisfy `read_line`. When the user presses
Return, a newline character is added to the string. For example, if the user
types 5 and presses return, `guess` looks like this: `5\n`. The `\n` represents
“newline,” the return key. The `trim` method eliminates `\n`, resulting in just
`5`.

The [`parse` method on strings][parse]<!-- ignore --> parses a string into some
kind of number. Because this method can parse a variety of number types, we
need to tell Rust the exact number type we want by using `let guess: u32`. The
colon (`:`) after `guess` tells Rust we’ll annotate the variable’s type. Rust
has a few built-in number types; the `u32` seen here is an unsigned, 32-bit
integer. It’s a good default choice for a small positive number. You’ll learn
about other number types in Chapter 3. Additionally, the `u32` annotation in
this example program and the comparison with `secret_number` means that Rust
will infer that `secret_number` should be a `u32` as well. So now the
comparison will be between two values of the same type!

[parse]: ../../std/primitive.str.html#method.parse

The call to `parse` could easily cause an error. If, for example, the string
contained `A👍%`, there would be no way to convert that to a number. Because it
might fail, the `parse` method returns a `Result` type, much like the
`read_line` method does as discussed earlier in “Handling Potential Failure
with the Result Type”. We’ll treat this `Result` the same way by
using the `expect` method again. If `parse` returns an `Err` `Result` variant
because it couldn’t create a number from the string, the `expect` call will
crash the game and print the message we give it. If `parse` can successfully
convert the string to a number, it will return the `Ok` variant of `Result`,
and `expect` will return the number that we want from the `Ok` value.

Let’s run the program now!

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.

We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!

## Allowing Multiple Guesses with Looping

The `loop` keyword gives us an infinite loop. Add that now to give users more
chances at guessing the number:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => println!("You win!"),
        }
    }
}
```

As you can see, we’ve moved everything into a loop from the guess input prompt
onward. Be sure to indent those lines another four spaces each, and run the
program again. Notice that there is a new problem because the program is doing
exactly what we told it to do: ask for another guess forever! It doesn’t seem
like the user can quit!

The user could always halt the program by using the keyboard shortcut `Ctrl-C`.
But there’s another way to escape this insatiable monster that we mentioned in
the `parse` discussion in “Comparing the Guess to the Secret Number”: if the user
enters a non-number answer, the program will crash. The user can take advantage
of that in order to quit, as shown here:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```

Typing `quit` actually quits the game, but so will any other non-number input.
However, this is suboptimal to say the least. We want the game to automatically
stop when the correct number is guessed.

### Quitting After a Correct Guess

Let’s program the game to quit when the user wins by adding a `break`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

By adding the `break` line after `You win!`, the program will exit the loop
when the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.

### Handling Invalid Input

To further refine the game’s behavior, rather than crashing the program when
the user inputs a non-number, let’s make the game ignore a non-number so the
user can continue guessing. We can do that by altering the line where `guess` is
converted from a `String` to a `u32`:

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

Switching from an `expect` call to a `match` expression is how you generally
move from crash on error to actually handling the error. Remember that `parse`
returns a `Result` type, and `Result` is an enum that has the variants `Ok` or
`Err`. We’re using a `match` expression here, like we did with the `Ordering`
result of the `cmp` method.

If `parse` is able to successfully turn the string into a number, it will return
an `Ok` value that contains the resulting number. That `Ok` value will match the
first arm’s pattern, and the `match` expression will just return the `num` value
that `parse` produced and put inside the `Ok` value. That number will end up
right where we want it in the new `guess` variable we’re creating.

If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does match
the `Err(_)` pattern in the second arm. The `_` is a catchall value; in this
example, we’re saying we want to match all `Err` values, no matter what
information they have inside them. So the program will execute the second arm’s
code, `continue`, which means to go to the next iteration of the `loop` and ask
for another guess. So effectively, the program ignores all errors that `parse`
might encounter!

Now everything in the program should work as expected. Let’s try it by running
`cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Awesome! With one tiny final tweak, we will finish the guessing game: recall
that the program is still printing out the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-5 shows the final code:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less    => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal   => {
                println!("You win!");
                break;
            }
        }
    }
}
```

<span class="caption">Listing 2-5: Complete code of the guessing game</span>

## Summary

At this point, you’ve successfully built the guessing game! Congratulations!

This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, methods, associated functions, using external crates, and more.
In the next few chapters, you’ll learn about these concepts in more detail.
Chapter 3 covers concepts that most programming languages have, such as
variables, data types, and functions, and shows how to use them in Rust.
Chapter 4 explores ownership, which is a Rust feature that is most different
from other languages. Chapter 5 discusses structs and method syntax, and
Chapter 6 endeavors to explain enums.
