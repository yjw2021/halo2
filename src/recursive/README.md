# Recursive Verifier

## verify process

we need to implement the whole verify process via halo2 circuit. The following steps are from `plonk/verifier.rs`

### preparing

- [ ] check instance column numbers
- [ ] read all instance commitments
- [ ] hash vkey into transcript
- [ ] hash all the `instance commitments` into transcript
- [ ] read `advice commitment` from transcript reader 
- [ ] hash `advice commitment` into transcript state
- [ ] sample `theta`
- [ ] read `lookup commitment` from transcript reader
- [ ] hash `lookup commitment` into transcript state
- [ ] sample `beta`, `gamma`
- [ ] read `columns / chunk_len` number of `permutation commitment` points from transcript
- [ ] hash `columns / chunk_len` number of `permutation commitment` points into transcript
- [ ] read `lookup product commitment` from transcript
- [ ] hash `lookup product commitment` into transcript
- [ ] read `random poly commitment` from transcript
- [ ] hash `random poly commitment` into transcript
- [ ] sample `y`
- [ ] read `h commitments` from transcript
- [ ] hash `h commitments` into transcript
- [ ] sample `x`
- [ ] read `instance evaluations` from transcript
- [ ] hash `instance evaluations` into transcript
- [ ] read `advice evaluations` from transcript
- [ ] hash `advice evaluations` into transcript
- [ ] read `fixed evaluations` from transcript
- [ ] hash `fixed evaluations` into transcript
- [ ] read `random evaluation` from transcript
- [ ] hash `random evaluation` into transcript
- [ ] read all `permutation evaluation` from transcript
- [ ] hash all `permutation evaluation` into transcript
- [ ] read all `permutation product evaluation` from transcript
- [ ] hash all `permutation product evaluation` into transcript
- [ ] read all `lookup product evaluation` from transcript
- [ ] hash all `lookup product evaluation` into transcript

### calculating

- [ ] compute `x^n`
- [ ] compute the necessary `blinding_factors`
- [ ] compute `l_evals`, where `l_evals` is the evaluation of each `L_i(X)` at `x`
	- [ ] TODO
- [ ] check `l_evals.len() == 2 + blinding_factors`
- [ ] set `l_last = l_evals[0]`
- [ ] set `l_blind = \sum l_evals[1..(1 + blinding_factors)]`
- [ ] set `l_0 = l_evals[1 + blinding_factors]`
- [ ] computing `h(x)`
	- [ ] walk through to evaluate all gate polynomials
	- [ ] evaluate permutaion
		- [ ] check: `z_0(X) = 1` at `\omega^0`
		- [ ] check: `z_last(X) = 0 or 1` at `\omega^{last}`
		- [ ] checks: `z_i(X) = z_{i-1}(\omega^(last) X)` at `\omega^0`, wrapping around for each column
		- [ ] checks: `(1 - (l_last(X) + l_blind(X))) * (z_i(\omega X) \prod (p(X) + \beta s_i(X) + \gamma) - z_i(X) \prod (p(X) + \delta^i \beta X + \gamma)`
	- [ ] evaluate lookup
		- [ ] check: `l_0(X) * (1 - z'(X)) = 0`
		- [ ] check: `l_last(X) * (z(X)^2 - z(X)) = 0`
		- [ ] check: `(1 - (l_last(X) + l_blind(X))) * (z(\omega X) (a'(X) + \beta) (s'(X) + \gamma) - z(X) (\theta^{m-1} a_0(X) + ... + a_{m-1}(X) + \beta) (\theta^{m-1} s_0(X) + ... + s_{m-1}(X) + \gamma)) = 0`
		- [ ] check: `l_0(X) * (a'(X) - s'(X)) = 0`
		- [ ] check: `(1 - (l_last(X) + l_blind(X))) * (a′(X) − s′(X))⋅(a′(X) − a′(\omega^{-1} X)) = 0`
- [ ] verify vanishing
	- [ ] compute `h_eval = (\sum h_i(x) * y^i) / (x^n - 1)`
	- [ ] compute `h_commitment = (\sum h_i * x^n)`
- [ ] prepare queries 
	- [ ] chain instance queries
	- [ ] chain advice queries
	- [ ] chain permutation queries
	- [ ] chain lookup queries
	- [ ] chain fixed queries
	- [ ] chain permutation common queries
	- [ ] chain vanishing queries

### multi opening

- [ ] scale msm by a random factor
- [ ] sample `x_1`, `x_2`
- [ ] compute intermediate sets
- [ ] combining point set commitment and evals
- [ ] read `f_commitment` from transcript
- [ ] sample `x_3`
- [ ] read `point_set.len()` number of scalars from transcript
- [ ] combining interpolated `r_poly`, `r_eval` into `msm_eval`
- [ ] sample `x_4`
- [ ] the final commitment is `msm` folded by `q_commitment`
- [ ] verify `msm_eval`, which can be achieved by pairing

## The common part

- [ ] read a point from the transcript
- [ ] read a scalar from the transcript
- [ ] hash a point into the transcript
- [ ] hash a scalar into the transcript
- [ ] sample a scalar according to the transcript current state
- [ ] 