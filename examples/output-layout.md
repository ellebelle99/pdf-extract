# Output layout

Illustrative only. The names and text are invented.

After `/pdf-extract` with output folder `papers` and base name `Smith_2020_Example_Paper`:

    papers/
      Smith_2020_Example_Paper_raw.md
      images/
        Fig. 1. Overview of the model.png
        Fig. 2. Results by group.png
        img-0-3.png

Figures with a caption line directly below them are renamed. Images with no caption nearby, such as a journal logo, keep their original names (`img-0-3.png` above).

A fragment of `Smith_2020_Example_Paper_raw.md`:

    ![](papers/images/Fig. 1. Overview of the model.png)

    **Fig. 1.** Overview of the model.

The skill prints the output path, the character count and the list of renamed figures when it finishes.
