# NAME

Perl::Critic::Policy::ProhibitPipeOpen - Don't let a pipe open be the quiet way to shell out.

# VERSION

version 1.000

# Perl::Critic::Policy::ProhibitPipeOpen

[Perl::Critic::Policy::logicLAB::ProhibitShellDispatch](https://metacpan.org/pod/Perl%3A%3ACritic%3A%3APolicy%3A%3AlogicLAB%3A%3AProhibitShellDispatch) flags `system`,
`exec`, `qx` and backticks, which is every obvious way to run an external
command -- and not the pipe open, which does the same thing and reads the
command's output besides:

```perl
open( my $fh, '-|', $bin, '--version' );   # not flagged by that policy
my $version = qx{$bin --version};          # flagged
```

Having one of those be quietly acceptable turns the linter into an argument for
writing the shell-out in whichever spelling it happens not to look at, rather
than a decision somebody made.  This policy closes that off, so that every way
of starting a process needs the same explicit `## no critic` and the comment
that ought to go with it.

It is not that a pipe open is worse.  In the list form it is the better of the
two -- no shell, so no quoting to get wrong.  It is that either one should be
on the record.

Modelled on [Perl::Critic::Policy::InputOutput::ProhibitTwoArgOpen](https://metacpan.org/pod/Perl%3A%3ACritic%3A%3APolicy%3A%3AInputOutput%3A%3AProhibitTwoArgOpen), which has
the inverse job: it _exempts_ the fork handles this policy exists to find.

## PROHIBITED

```perl
open( my $fh, '-|', 'ls', '-l' );   # three-arg, read from a command
open( my $fh, '|-', 'mail', $to );  # three-arg, write to a command
open( my $fh, '-|' );               # two-arg fork
open( FH, 'ls -l |' );              # two-arg, the old spelling
open( FH, '| mail bob' );
```

## ALLOWED

Ordinary file opens, which is every other thing `open` does:

```perl
open( my $fh, '<',  $path );
open( my $fh, '>>', $path );
open( my $fh, '<',  \$scalar );
```

## CONFIGURATION

This policy is not configurable except for the standard options.

## CAVEATS

The mode has to be a literal for this to see it.  A pipe open assembled at
runtime, `open( $fh, $mode, $cmd )` with `$mode` computed, goes
unnoticed: the policy reads source, not intent.

## METHODS

### violates

Standard [Perl::Critic::Policy](https://metacpan.org/pod/Perl%3A%3ACritic%3A%3APolicy) interface.  Returns a violation for an `open`
whose mode says to spawn a process, and nothing for one that opens a file.

# BUGS

Please report any bugs or feature requests on the bugtracker website
[https://github.com/teodesian/perl-critic-policy-prohibitpipeopen/issues](https://github.com/teodesian/perl-critic-policy-prohibitpipeopen/issues)

When submitting a bug or request, please include a test-file or a
patch to an existing test-file that illustrates the bug or desired
feature.

# AUTHORS

Current Maintainers:

- George S. Baugh <teodesian@gmail.com>

# CONTRIBUTOR

George Baugh <andy@troglodyne.net>

# COPYRIGHT AND LICENSE

Copyright (c) 2026 Troglodyne LLC

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
