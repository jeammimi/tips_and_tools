#
smt init

smt configure -l parameters   #To tell to put label in parameters
smt configure -d data/raw


# Ex of run:
smt run -m src/tests_auto/make_test.py src/tests_auto/params.json weights=data/cluster/ref-2d-hidden-50/weights.999-2.04.hdf5 --label="test1"


#Boiler plate to smt main script:


import importlib
from pathlib import Path


def import_parents(level=1):
    global __package__
    file = Path(__file__).resolve()
    parent, top = file.parent, file.parents[level]
    __package__ = '.'.join(parent.parts[len(top.parts):])
    sys.path.append(str(top))
    importlib.import_module(__package__)  # won't be needed after that

def load_parameters(filename):
    with open(filename, "r") as f:
        traj = json.load(f)
    return traj


if __name__ == "__main__":

    import_parents(level=2)
    from .automated_test import Brownian_V_separation

    from ..models.build_model import build_model
    param_file = sys.argv[1]
    parameters = load_parameters(param_file)
    # print(sys.argv)
    if "sumatra_label" in parameters:
        parameters["data_folder"] = os.path.join(parameters["data_folder"],
                                                 parameters["sumatra_label"])
        parameters.pop("sumatra_label")
    else:
        print("no extra label")

    with open(os.path.join(parameters["data_folder"], "params.json"), "w") as f:
        s = json.dumps(parameters)
        f.write(s)

