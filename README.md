# circom

ark-circom
Arkworks bindings to Circom's R1CS, for Groth16 Proof and Witness generation in Rust.

Github Actions

Documentation
Clone the repository and run cd ark-circom/ && cargo doc --open

Add ark-circom to your repository
[dependencies]

ark-circom = { git = "https://github.com/gakonst/ark-circom.git" }
Example
// Load the WASM and R1CS for witness and proof generation
let cfg = CircomConfig::<Bn254>::new(
    "./test-vectors/mycircuit.wasm",
    "./test-vectors/mycircuit.r1cs",
)?;

// Insert our public inputs as key value pairs
let mut builder = CircomBuilder::new(cfg);
builder.push_input("a", 3);
builder.push_input("b", 11);

// Create an empty instance for setting it up
let circom = builder.setup();

// Run a trusted setup
let mut rng = thread_rng();
let params = generate_random_parameters_with_reduction(circom, &mut rng)?;

// Get the populated instance of the circuit with the witness
let circom = builder.build()?;

let inputs = circom.get_public_inputs().unwrap();

// Generate the proof
let proof = prove(&params, circom, &mut rng)?;

// Check that the proof is valid
let pvk = process_vk(&params.vk)?;
let verified = verify_with_processed_vk(&pvk, &inputs, &proof)?;
assert!(verified);
Running the tests
Tests require the following installed:

solc. We also recommend using solc-select for more flexibility.
ganache-cli
Features
 Witness generation using Circom's WASM witness code
 ZKey parsing into Arkworks Proving Key over BN254
 Compatibility layer for Ethereum types, so that proofs can be used in Solidity verifiers
 Proof generations and verification using Arkworks
 CLI for common operations
Known limitations
Currently, due to an issue in our upstream (wasmerio/wasmer#4072), this crate works as expected only up to Rust version 1.67.0; in newer Rust versions, wasmer is currently unsound.

Acknowledgements
This library would not have been possibly without the great work done in:

zkutil
snarkjs
Special shoutout to Kobi Gurkan for all the help in parsing SnarkJS' ZKey file format.
