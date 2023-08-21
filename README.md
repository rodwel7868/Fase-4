# Fase-4

package Clases;

import java.sql.Connection;
import java.sql.DriverManager;
import javax.swing.JOptionPane;

public class CConexion {
    
    Connection conectar;
    
    String usuario = "root";
    String contrasena = "Batman2021";
    String bd = "login";
    String ip = "localhost";
    String puerto = "3306";
    
    String cadena = "jdbc:mysql://"+ip+":"+puerto+"/"+bd;
    
    public Connection estableceConexion(){
        
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conectar = DriverManager.getConnection(cadena,usuario, contrasena);
           // JOptionPane.showMessageDialog(null, "Conexión exitosa a la base de datos");
        }catch (Exception e) {
            JOptionPane.showMessageDialog(null, "Problema en la conexion"+ e.toString());
        }
        return conectar;
    }
}

package Clases;

import com.mycompany.Testi.FormEmpleado;
import com.mysql.cj.xdevapi.PreparableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import javax.swing.JOptionPane;
import javax.swing.JPasswordField;
import javax.swing.JTextField;
import textiles.formMenuPrincipal;

public class CLogin {
    
    public void validaUsuario(JTextField usuario, JPasswordField contrasena){
        
        try {
            ResultSet rs= null;
            PreparedStatement ps=null;
            
            Clases.CConexion objetoConexion = new Clases.CConexion();
            
            String consulta="select * from Usuarios where Usuarios.ingresoUsuario = (?) and Usuarios.ingresoContrasena = (?);";
            ps = objetoConexion.estableceConexion().prepareStatement(consulta);
            
            String contra = String.valueOf(contrasena.getPassword());
            
            ps.setString(1, usuario.getText());
            ps.setString(2, contra);
            
            rs = ps.executeQuery();
            
            if (rs.next()) {
                
                JOptionPane.showMessageDialog(null, "El usuario es correcto");
                FormEmpleado objMenu = new FormEmpleado();
                objMenu.setVisible(true);
                
            }
            else{
                JOptionPane.showMessageDialog(null, "El usuario es incorrecto");
            }

        } catch (Exception e) {
            
            JOptionPane.showMessageDialog(null, "Error"+ e.toString());
        }   
    }
}

package com.mycompany.Testi;

import java.sql.Connection;
import java.sql.DriverManager;
import javax.swing.JOptionPane;


public class CConexion {
    
    Connection conectar;
    
    String usuario ="root";
    String contrasenia ="Batman2021";
    String bd ="Empleados";
    String ip ="localhost";
    String puerto ="3306";
    
    String cadena = "jdbc:mysql://"+ip+":"+puerto+"/"+bd;
    
    public Connection estableceConexion(){
        try {
            
            Class.forName("com.mysql.cj.jdbc.Driver");
            conectar = DriverManager.getConnection(cadena,usuario, contrasenia);
            //JOptionPane.showMessageDialog(null, "Conexón exitosa a la base de datos.");
            
        } catch (Exception e) {
            JOptionPane.showMessageDialog(null,"Error de conexión a la base de datos, error: "+ e.toString());
        }return conectar;
    }
    
}


package com.mycompany.Testi;

import com.mysql.cj.xdevapi.Result;
import java.sql.CallableStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import javax.swing.JOptionPane;
import javax.swing.JTable;
import javax.swing.JTextField;
import javax.swing.table.DefaultTableModel;
import javax.swing.table.TableModel;
import javax.swing.table.TableRowSorter;


public class CEmpleados {
    
    int nEmpleado;
    int id;
    int fecha;
    String nombreEmpleados;
    String apellidosEmpleados;
    int cantidad;
    
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getnEmpleado() {
        return nEmpleado;
    }

    public void setnEmpleado(int nEmpleado) {
        this.nEmpleado = nEmpleado;
    }

    public String getNombreEmpleados() {
        return nombreEmpleados;
    }

    public void setNombreEmpleados(String nombreEmpleados) {
        this.nombreEmpleados = nombreEmpleados;
    }

    public String getApellidosEmpleados() {
        return apellidosEmpleados;
    }

    public void setApellidosEmpleados(String apellidosEmpleados) {
        this.apellidosEmpleados = apellidosEmpleados;
    }

    public int getCantidad() {
        return cantidad;
    }

    public void setCantidad(int cantidad) {
        this.cantidad = cantidad;
    }
    
    public void InsertarEmpleado(JTextField pNEmpleado, JTextField pNombres, JTextField pApellidos){
        
        setnEmpleado(Integer.parseInt(pNEmpleado.getText()));
        setNombreEmpleados(pNombres.getText());
        setApellidosEmpleados(pApellidos.getText());
        
        CConexion objetoConexion = new CConexion();
        
        String consulta = "insert into emplea(nEmpleado, nombres, apellidos) value (?, ?, ?);";
        
        try {
            
            CallableStatement cs = objetoConexion.estableceConexion().prepareCall(consulta);
            
            cs.setInt(1, getnEmpleado());
            cs.setString(2, getNombreEmpleados());
            cs.setString(3, getApellidosEmpleados());
            
            cs.execute();
            
            JOptionPane.showMessageDialog(null,"Se inserto correctamente el empleado");
            
        } catch (Exception e) {
            JOptionPane.showMessageDialog(null, "No se inserto correctamente el emleado error: " +e.toString());
        }
    }
    
    public void MostrarEmpleados(JTable pTablaTotalEmpleados){
        
        CConexion objetoConexion = new CConexion();
        
        DefaultTableModel modelo = new DefaultTableModel();
        
        TableRowSorter<TableModel> ordenarTabla = new TableRowSorter<TableModel> (modelo);
        pTablaTotalEmpleados.setRowSorter(ordenarTabla);
        
        String sql = "";
        
        modelo.addColumn("Id");
        modelo.addColumn("nEmpleado");
        modelo.addColumn("Nombres");
        modelo.addColumn("Apellidos");
        
        pTablaTotalEmpleados.setModel(modelo);
        
        sql = "select * from emplea;";
        
        String[] datos = new String[4];
        
        Statement st;
        
        try {
            
            st = objetoConexion.estableceConexion().createStatement();
            
            ResultSet rs = st.executeQuery(sql);
            
            while(rs.next()){
                
                datos[0] = rs.getString(1);
                datos[1] = rs.getString(2);
                datos[2] = rs.getString(3);
                datos[3] = rs.getString(4);
                
                modelo.addRow(datos);
            }
            
            pTablaTotalEmpleados.setModel(modelo);
            
        } catch (Exception e) {
            
            JOptionPane.showMessageDialog(null,"No se puedo mostrar los registros, error: "+ e.toString());
        }
    }
    
    public void SeleccionarEmpleado(JTable pTablaEmpleado, JTextField pId, JTextField pNEmpleado, JTextField pNombre, JTextField pApellidos){
        
        try {
            int fila = pTablaEmpleado.getSelectedRow();
            
            if(fila >=0){
                
                pId.setText((pTablaEmpleado.getValueAt(fila, 0).toString()));
                pNEmpleado.setText((pTablaEmpleado.getValueAt(fila, 1).toString()));
                pNombre.setText((pTablaEmpleado.getValueAt(fila, 2).toString()));
                pApellidos.setText((pTablaEmpleado.getValueAt(fila, 3).toString()));
                
            }
            else{
                JOptionPane.showMessageDialog(null, "Fila no seleccionada.");
            }
        } catch (Exception e) {
            
            JOptionPane.showMessageDialog(null, "Error de selección, error: "+ e.toString());
            
        }
    }
    
    public void ModificarEmpleado(JTextField pId, JTextField pNEmpleado, JTextField pNombres, JTextField pApellidos){
        
        setId(Integer.parseInt(pId.getText()));
        setnEmpleado(Integer.parseInt(pNEmpleado.getText()));
        setNombreEmpleados(pNombres.getText());
        setApellidosEmpleados(pApellidos.getText());
        
        CConexion objetoConexion = new CConexion();
        
        String consulta = "UPDATE emplea SET Emplea.nEmpleado = ?, Emplea.nombres = ?, Emplea.apellidos= ? WHERE Emplea.id=?;";
        
        try {
            CallableStatement cs = objetoConexion.estableceConexion().prepareCall(consulta);
            
            cs.setInt(1, getnEmpleado());
            cs.setString(2, getNombreEmpleados());
            cs.setString(3, getApellidosEmpleados());
            cs.setInt(4, getId());
            
            cs.execute();
            
            JOptionPane.showMessageDialog(null, "Modificación exitosa.");
            
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(null, "Error de modificación, error: "+e.toString());
        }
        
    }
    
    public void EliminarEmpleado(JTextField pNEmpleado){
     
        setnEmpleado(Integer.parseInt(pNEmpleado.getText()));
        
        CConexion objetoConexion = new CConexion();
        
        String consulta = "DELETE FROM emplea where emplea.id=?;";
        
        try {
            
            CallableStatement cs = objetoConexion.estableceConexion().prepareCall(consulta);
            cs.setInt(1, getnEmpleado());
            cs.execute();
            
            JOptionPane.showMessageDialog(null,"Empleado eliminado exitosamente.");

        } catch (SQLException e) {
            
            JOptionPane.showMessageDialog(null,"Error de empleado eliminado, error."+e.toString());
            
        }
    }
    
    public void buscarPnEmpleado(String str){
        
    }
}


package com.mycompany.Testi;

public class FormEmpleado extends javax.swing.JFrame {

    public FormEmpleado() {
        initComponents();
        
        this.setLocationRelativeTo(null);
        txtId.setEnabled(false);
        
        CEmpleados objetoEmpleados = new CEmpleados();
        objetoEmpleados.MostrarEmpleados(tbTotalEmpleados);
        
    }

    @SuppressWarnings("unchecked")
    // <editor-fold defaultstate="collapsed" desc="Generated Code">                          
    private void initComponents() {

        jPanel1 = new javax.swing.JPanel();
        jLabel1 = new javax.swing.JLabel();
        jLabel2 = new javax.swing.JLabel();
        txtId = new javax.swing.JTextField();
        txtnEmpleado = new javax.swing.JTextField();
        jLabel3 = new javax.swing.JLabel();
        txtNombres = new javax.swing.JTextField();
        btnGuardar = new javax.swing.JButton();
        btnModificar = new javax.swing.JButton();
        btnEliminar = new javax.swing.JButton();
        jLabel4 = new javax.swing.JLabel();
        txtApellidos = new javax.swing.JTextField();
        jLabel5 = new javax.swing.JLabel();
        txtCantidad = new javax.swing.JTextField();
        jLabel6 = new javax.swing.JLabel();
        txtFecha = new javax.swing.JTextField();
        jPanel2 = new javax.swing.JPanel();
        jScrollPane1 = new javax.swing.JScrollPane();
        tbTotalEmpleados = new javax.swing.JTable();

        setDefaultCloseOperation(javax.swing.WindowConstants.EXIT_ON_CLOSE);

        jPanel1.setBorder(javax.swing.BorderFactory.createTitledBorder("Empleados"));

        jLabel1.setText("Id:");

        jLabel2.setText("nEmpleado:");

        txtId.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                txtIdActionPerformed(evt);
            }
        });

        jLabel3.setText("Nombres:");

        btnGuardar.setText("Guardar");
        btnGuardar.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                btnGuardarActionPerformed(evt);
            }
        });

        btnModificar.setText("Modificar");
        btnModificar.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                btnModificarActionPerformed(evt);
            }
        });

        btnEliminar.setText("Eliminar");
        btnEliminar.addActionListener(new java.awt.event.ActionListener() {
            public void actionPerformed(java.awt.event.ActionEvent evt) {
                btnEliminarActionPerformed(evt);
            }
        });

        jLabel4.setText("Apellidos:");

        jLabel5.setText("Cantidad:");

        jLabel6.setText("Fecha:");

        javax.swing.GroupLayout jPanel1Layout = new javax.swing.GroupLayout(jPanel1);
        jPanel1.setLayout(jPanel1Layout);
        jPanel1Layout.setHorizontalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel1Layout.createSequentialGroup()
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                    .addComponent(btnEliminar, javax.swing.GroupLayout.Alignment.TRAILING, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
                    .addComponent(btnModificar, javax.swing.GroupLayout.Alignment.TRAILING, javax.swing.GroupLayout.DEFAULT_SIZE, 122, Short.MAX_VALUE)
                    .addComponent(btnGuardar, javax.swing.GroupLayout.Alignment.TRAILING, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
                    .addComponent(txtnEmpleado, javax.swing.GroupLayout.Alignment.TRAILING)
                    .addComponent(txtNombres)
                    .addComponent(txtApellidos)
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addComponent(jLabel2)
                        .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED, 66, Short.MAX_VALUE))
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addComponent(jLabel6)
                        .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                        .addComponent(txtFecha))
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                            .addComponent(jLabel3)
                            .addComponent(jLabel4)
                            .addGroup(jPanel1Layout.createSequentialGroup()
                                .addComponent(jLabel5)
                                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                                .addComponent(txtCantidad, javax.swing.GroupLayout.PREFERRED_SIZE, 30, javax.swing.GroupLayout.PREFERRED_SIZE)))
                        .addGap(0, 0, Short.MAX_VALUE))
                    .addGroup(jPanel1Layout.createSequentialGroup()
                        .addComponent(jLabel1)
                        .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                        .addComponent(txtId)))
                .addContainerGap())
        );
        jPanel1Layout.setVerticalGroup(
            jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel1Layout.createSequentialGroup()
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.BASELINE)
                    .addComponent(jLabel1)
                    .addComponent(txtId, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE))
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.UNRELATED)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.BASELINE)
                    .addComponent(jLabel6)
                    .addComponent(txtFecha, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE))
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
                .addComponent(jLabel2)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(txtnEmpleado, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(jLabel3)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(txtNombres, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(jLabel4)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(txtApellidos, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addGroup(jPanel1Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.BASELINE)
                    .addComponent(jLabel5)
                    .addComponent(txtCantidad, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE))
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(btnGuardar)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(btnModificar)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(btnEliminar)
                .addGap(18, 18, 18))
        );

        jPanel2.setBorder(javax.swing.BorderFactory.createTitledBorder("Lista de Empleados"));

        tbTotalEmpleados.setModel(new javax.swing.table.DefaultTableModel(
            new Object [][] {
                {},
                {},
                {},
                {}
            },
            new String [] {

            }
        ));
        tbTotalEmpleados.addMouseListener(new java.awt.event.MouseAdapter() {
            public void mouseClicked(java.awt.event.MouseEvent evt) {
                tbTotalEmpleadosMouseClicked(evt);
            }
        });
        jScrollPane1.setViewportView(tbTotalEmpleados);

        javax.swing.GroupLayout jPanel2Layout = new javax.swing.GroupLayout(jPanel2);
        jPanel2.setLayout(jPanel2Layout);
        jPanel2Layout.setHorizontalGroup(
            jPanel2Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(javax.swing.GroupLayout.Alignment.TRAILING, jPanel2Layout.createSequentialGroup()
                .addContainerGap()
                .addComponent(jScrollPane1, javax.swing.GroupLayout.DEFAULT_SIZE, 568, Short.MAX_VALUE)
                .addContainerGap())
        );
        jPanel2Layout.setVerticalGroup(
            jPanel2Layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(jPanel2Layout.createSequentialGroup()
                .addComponent(jScrollPane1, javax.swing.GroupLayout.DEFAULT_SIZE, 264, Short.MAX_VALUE)
                .addContainerGap())
        );

        javax.swing.GroupLayout layout = new javax.swing.GroupLayout(getContentPane());
        getContentPane().setLayout(layout);
        layout.setHorizontalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(layout.createSequentialGroup()
                .addContainerGap()
                .addComponent(jPanel1, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                .addPreferredGap(javax.swing.LayoutStyle.ComponentPlacement.RELATED)
                .addComponent(jPanel2, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)
                .addContainerGap())
        );
        layout.setVerticalGroup(
            layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
            .addGroup(layout.createSequentialGroup()
                .addGap(19, 19, 19)
                .addGroup(layout.createParallelGroup(javax.swing.GroupLayout.Alignment.LEADING)
                    .addGroup(layout.createSequentialGroup()
                        .addComponent(jPanel2, javax.swing.GroupLayout.PREFERRED_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.PREFERRED_SIZE)
                        .addContainerGap())
                    .addComponent(jPanel1, javax.swing.GroupLayout.DEFAULT_SIZE, javax.swing.GroupLayout.DEFAULT_SIZE, Short.MAX_VALUE)))
        );

        pack();
    }// </editor-fold>                        

    private void txtIdActionPerformed(java.awt.event.ActionEvent evt) {                                      
        // TODO add your handling code here:
    }                                     

    private void btnGuardarActionPerformed(java.awt.event.ActionEvent evt) {                                           
        
        CEmpleados objetoEmpleado = new CEmpleados();
        objetoEmpleado.InsertarEmpleado(txtnEmpleado, txtNombres, txtApellidos);
        objetoEmpleado.MostrarEmpleados(tbTotalEmpleados);
    }                                          

    private void tbTotalEmpleadosMouseClicked(java.awt.event.MouseEvent evt) {                                              
        
        CEmpleados objetoEmpleado = new CEmpleados();
        objetoEmpleado.SeleccionarEmpleado(tbTotalEmpleados, txtId, txtnEmpleado, txtNombres, txtApellidos);
        
    }                                             

    private void btnModificarActionPerformed(java.awt.event.ActionEvent evt) {                                             
        
        CEmpleados objetoEmpleado = new CEmpleados();
        objetoEmpleado.ModificarEmpleado(txtId, txtnEmpleado, txtNombres, txtApellidos);
        objetoEmpleado.MostrarEmpleados(tbTotalEmpleados);
        
        
    }                                            

    private void btnEliminarActionPerformed(java.awt.event.ActionEvent evt) {                                            
        
        CEmpleados objetoEmpleado = new CEmpleados();
        objetoEmpleado.EliminarEmpleado(txtId);
        objetoEmpleado.MostrarEmpleados(tbTotalEmpleados);

        
    }                                           

    public static void main(String args[]) {
        /* Set the Nimbus look and feel */
        //<editor-fold defaultstate="collapsed" desc=" Look and feel setting code (optional) ">
        /* If Nimbus (introduced in Java SE 6) is not available, stay with the default look and feel.
         * For details see http://download.oracle.com/javase/tutorial/uiswing/lookandfeel/plaf.html 
         */
        try {
            for (javax.swing.UIManager.LookAndFeelInfo info : javax.swing.UIManager.getInstalledLookAndFeels()) {
                if ("Nimbus".equals(info.getName())) {
                    javax.swing.UIManager.setLookAndFeel(info.getClassName());
                    break;
                }
            }
        } catch (ClassNotFoundException ex) {
            java.util.logging.Logger.getLogger(FormEmpleado.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (InstantiationException ex) {
            java.util.logging.Logger.getLogger(FormEmpleado.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (IllegalAccessException ex) {
            java.util.logging.Logger.getLogger(FormEmpleado.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        } catch (javax.swing.UnsupportedLookAndFeelException ex) {
            java.util.logging.Logger.getLogger(FormEmpleado.class.getName()).log(java.util.logging.Level.SEVERE, null, ex);
        }
        //</editor-fold>
        //</editor-fold>
        //</editor-fold>
        //</editor-fold>

        /* Create and display the form */
        java.awt.EventQueue.invokeLater(new Runnable() {
            public void run() {
                new FormEmpleado().setVisible(true);
            }
        });
    }

    // Variables declaration - do not modify                     
    private javax.swing.JButton btnEliminar;
    private javax.swing.JButton btnGuardar;
    private javax.swing.JButton btnModificar;
    private javax.swing.JLabel jLabel1;
    private javax.swing.JLabel jLabel2;
    private javax.swing.JLabel jLabel3;
    private javax.swing.JLabel jLabel4;
    private javax.swing.JLabel jLabel5;
    private javax.swing.JLabel jLabel6;
    private javax.swing.JPanel jPanel1;
    private javax.swing.JPanel jPanel2;
    private javax.swing.JScrollPane jScrollPane1;
    private javax.swing.JTable tbTotalEmpleados;
    private javax.swing.JTextField txtApellidos;
    private javax.swing.JTextField txtCantidad;
    private javax.swing.JTextField txtFecha;
    private javax.swing.JTextField txtId;
    private javax.swing.JTextField txtNombres;
    private javax.swing.JTextField txtnEmpleado;
    // End of variables declaration                   
}


package com.mycompany.Testi;


public class inicio {
    
    public static void main(String[] args) {
        FormEmpleado objetoFormEmpleado = new FormEmpleado();
        objetoFormEmpleado.setVisible(true);
    }
    
}
