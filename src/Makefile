CC=gcc -std=c11
CFLAGS=-Wall -Wextra -Werror
FLAG_COV=--coverage
S21_FUNC_TESTS_C=tests/*_test.c
VALGRIND_FLAGS=--tool=memcheck --track-origins=yes

OS=$(shell uname -s)

ifeq ($(OS),Linux)
	TEST_LIBS=-lcheck -lm -lsubunit
else
	TEST_LIBS=`pkg-config --cflags --libs check`
endif

all: gcov_report

s21_math.a:
	$(CC) $(CFLAGS) -c s21_math.c
	ar rc $@ *.o
	ranlib $@

test: s21_math.c
	$(CC) $(CFLAGS) -o s21_math tests/main.c $(S21_FUNC_TESTS_C) $^ $(FLAG_COV) $(TEST_LIBS)
	./s21_math

gcov_report: test
	mkdir report
ifeq ($(OS),Linux)
	mv s21_math*.g* report/
else
	mv s21_math*.g* report/
endif
	rm -f *.gcda *.gcno
	lcov -c -d report -o gcov.info
	genhtml gcov.info -o gcov_report

gcov_macos:
	curl -fsSL https://rawgit.com/kube/42homebrew/master/install.sh | zsh
	brew install lcov

format:
	@cp ../materials/linters/.clang-format ./
	@clang-format -i *.c *.h tests/*.c tests/*.h
	@clang-format -n *.c *.h tests/*.c tests/*.h
	@rm .clang-format

valgrind: clean
	$(CC) $(CFLAGS) -o s21_math tests/main.c $(S21_FUNC_TESTS_C) -g -O0 *.c $(TEST_LIBS)
	valgrind $(VALGRIND_FLAGS) ./s21_math

clean:
	rm -rf gcov_report/ report/
	rm -f *.info s21_math
	rm -f *.gcda *.gcno *.gcov
	rm -f s21_math.a s21_math.o

rebuild: clean all
