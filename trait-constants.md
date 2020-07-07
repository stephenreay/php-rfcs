# Trait Constants

Traits are intended to allow code re-use across classes, but currently do not allow the definition of constants, which limits their ability to provide some types of functionality in a "clean" manner.

## Proposal

Allow traits to declare constants, in a similar fashion to classes and interfaces.

### Access

Trait constants must be accessed via either the name of a class that uses the trait, or via the `self` keyword, e.g. from within the trait itself, or within a class that uses it.

Trait constants cannot be accessed directly via the name of the trait. (See https://wiki.php.net/rfc/deprecations_php_8_0#accessing_static_members_on_traits)

### Conflicts

As with properties of traits, the values of overlapping trait constants used in a class must be compatible, i.e. they must have the same value, otherwise they are considered conflicting, and a fatal error is produced. Conflict resolution (using the `as` or `insteadof` operators) is not supported. 

### Changing constant visibility

As with methods of traits, constant visibility within the class can be changed using the `as` operator.


### Examples

	trait Foo {
		
		public const FLAG_1 = 1;
		
		public const FLAG_2 = 2;
	
		public function doFoo(int $flags): void {
			if ($flags & self::FLAG_1 === self::FLAG_1) {
				echo 'Got flag 1';
			}
			if ($flags & self::FLAG_2 === self::FLAG_2) {
				echo 'Got flag 2';
			}
		}
	}
	
	trait Bar {
		protected const FLAG_1 = 'one';
		
		protected function doBar(string $flag): void {
			switch ($flag) {
				case self::FLAG_1:
					echo 'Got flag one';
				break;
			}
		}
	}
	
	trait Baz {
		use Foo;
		
		public const FLAG_2 = 2;
		
		...
	}
	
	
	// Legal:
	
	class A {
		use Foo;
	}
	
	class B {
		use Bar {
			FLAG_1 as public;
		}
	}
	
	class C {
		use Foo, Baz;
	}
	

	// Fatal Error

	class D {
		use Foo, Bar;
	}
	
	class E {
		use Foo;
		
		protected const FLAG_2 = 'two';
	}
	

	
	
