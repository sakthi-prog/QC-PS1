#QS PS
    
    
    import math 
    import cmath

    class QuantumCircuit:
        def __init__(self,n_qubits):
            self.n = n_qubits
            self.dim = 2**n_qubits
            self.state = [0.0 + 0.0j for _ in range(self.dim)]
            self.state[0] = 1.0 + 0.0j #initialize as |00.....0>
    
    # Pauli-X
    @staticmethod
    def X():
        return [[0, 1], [1, 0]]

    # Pauli-Y
    @staticmethod
    def Y():
        return [[0, -1j], [1j, 0]]

    # Pauli-Z
    @staticmethod
    def Z():
        return [[1, 0], [0, -1]]

    # Hadamard
    @staticmethod
    def H():
        norm = 1 / math.sqrt(2)
        return [[norm, norm], [norm, -norm]]

    # Phase S
    @staticmethod
    def S():
        return [[1, 0], [0, 1j]]

    # Apply a single-qubit gate to the given qubit
    def apply_single(self, gate, qubit):
        new_state = [0.0 + 0.0j for _ in range(self.dim)]
        for i in range(self.dim):
            if ((i >> qubit) & 1) == 0:
                zero_idx = i
                one_idx = i | (1 << qubit)
                new_state[zero_idx] += gate[0][0] * self.state[zero_idx] + gate[0][1] * self.state[one_idx]
                new_state[one_idx] += gate[1][0] * self.state[zero_idx] + gate[1][1] * self.state[one_idx]
        self.state = new_state

    # Controlled-X (CNOT/CX)
    def apply_CX(self, control, target):
        new_state = [0.0 + 0.0j for _ in range(self.dim)]
        for i in range(self.dim):
            if ((i >> control) & 1) == 0:
                new_state[i] = self.state[i]
            else:
                flipped = i ^ (1 << target)
                new_state[flipped] = self.state[i]
        self.state = new_state

    # Controlled-Y (CY)
    def apply_CY(self, control, target):
        Y = QuantumCircuit.Y()
        new_state = [0.0 + 0.0j for _ in range(self.dim)]
        for i in range(self.dim):
            if ((i >> control) & 1) == 0:
                new_state[i] += self.state[i]
            else:
                if ((i >> target) & 1) == 0:
                    zero_idx = i
                    one_idx = i | (1 << target)
                    new_state[zero_idx] += Y[0][0] * self.state[zero_idx] + Y[0][1] * self.state[one_idx]
                    new_state[one_idx] += Y[1][0] * self.state[zero_idx] + Y[1][1] * self.state[one_idx]
                else:
                    zero_idx = i ^ (1 << target)
                    one_idx = i                    
        self.state = new_state

    # Controlled-Z (CZ)
    def apply_CZ(self, control, target):
        new_state = [0.0 + 0.0j for _ in range(self.dim)]
        for i in range(self.dim):
            if ((i >> control) & 1) == 0 or ((i >> target) & 1) == 0:
                new_state[i] = self.state[i]
            else:
                new_state[i] = -self.state[i]
        self.state = new_state

    def view_state(self):
        return self.state

    def measure(self):
        probs = [abs(amp)**2 for amp in self.state]
        result = probs.index(max(probs))
        return result
        
    qc=QuantumCircuit(2)
    qc.apply_single(QuantumCircuit.H(),0)
    qc.apply_single(QuantumCircuit.X(),1)
    qc.apply_single(QuantumCircuit.Y(),0)
    qc.apply_single(QuantumCircuit.Z(),1)                    #line 98 to 109 : example usage of gates
    qc.apply_single(QuantumCircuit.S(),0)
    qc.apply_CX(0,1)
    qc.apply_CY(1,0)
    qc.apply_CZ(0,1)

    print("final statevector: ", qc.view_state())
    print("measurement outcome: ", qc.measure())
    
    











