**********************************
How to make beautiful code in LSC?
**********************************

This page describes some tips to make comprehensive code into LSC sources.

Indentation: tabulation or space?
=================================

For indenting your code, configure your IDE to **only use the tabulation character** instead of spaces (some IDE simulates tabulation with spaces).

Example
=======

.. code-block::

    /**
     * Indentation
     */
    class Example {
    
    	int[] myArray = { 1, 2, 3, 4, 5, 6 };
    	int theInt = 1;
    	String someString = "Hello";
    	double aDouble = 3.0;
    
    	void foo(int a, int b, int c, int d, int e, int f) {
    		switch (a) {
    			case 0:
    				Other.doFoo();
    				break;
    			default:
    				Other.doBaz();
    		}
    	}
    
    	void bar(List v) {
    		for (int i = 0; i < 10; i++) {
    			v.add(new Integer(i));
    		}
    	}
    }


This codestyle should be applied on all structures: if, while, for, try/catch,...
