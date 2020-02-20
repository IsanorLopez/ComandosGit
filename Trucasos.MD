 # LIMITAR CARACTERES DE UN TEXTOBOX  
 ~~~c#
private void dgvGrid_Numeros_TextChanged(object sender, EventArgs e)
        {
            //Aqui se hacen las validaciones a los TextBox del Grid para los caracteres permitidos
            TextBox txtGrid = sender as TextBox;
            int iPosicionCursor = txtGrid.SelectionStart;

            switch (dg_informacion.Columns[dg_informacion.CurrentCell.ColumnIndex].Name)
            {
                case "P.VTA NORMAL":
                    {
                        txtGrid.MaxLength = 6;
                        txtGrid.Text = validarCaracteres(txtGrid.Text, Caracteres.numero, dg_informacion);
                        txtGrid.SelectionStart = iPosicionCursor;
                    }
                    break;

                case "MODELO":
                    txtGrid.MaxLength = 5;
                    txtGrid.Text = txtGrid.Text.Replace("'", "");
                    txtGrid.Text = validarCaracteres(txtGrid.Text, Caracteres.caracteresValidosYNumeros, dg_informacion);
                    txtGrid.SelectionStart = iPosicionCursor;
                    break;

                default:
                    break;
            }
        }
~~~
