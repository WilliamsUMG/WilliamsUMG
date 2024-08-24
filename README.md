import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.io.FileWriter;
import java.io.IOException;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;

public class CalculadoraFranco extends JFrame implements KeyListener {
    private JTextField display;
    private List<String> historial;

    public CalculadoraFranco() {
        setTitle("Calculadora");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setSize(300, 400);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout());

        historial = new ArrayList<>();

        display = new JTextField();
        display.setEditable(false);
        display.setFont(new Font("Arial", Font.PLAIN, 20));
        add(display, BorderLayout.NORTH);

        JPanel panelBotones = new JPanel(new GridLayout(5, 4));

        String[] buttonLabels = {
            "7", "8", "9", "/",
            "4", "5", "6", "*",
            "1", "2", "3", "-",
            "0", ".", "=", "+"
        };

        for (String label : buttonLabels) {
            JButton button = new JButton(label);
            button.setFont(new Font("Arial", Font.PLAIN, 20));
            button.addActionListener(new BotonListener());
            panelBotones.add(button);
        }

        add(panelBotones, BorderLayout.CENTER);

        JMenuBar menuBar = new JMenuBar();
        JMenu opcionesMenu = new JMenu("Opciones");
        JMenuItem nuevoItem = new JMenuItem("Nuevo");
        JMenuItem historicoItem = new JMenuItem("Histórico");
        opcionesMenu.add(nuevoItem);
        opcionesMenu.add(historicoItem);
        menuBar.add(opcionesMenu);

        JMenu ayudaMenu = new JMenu("Ayuda");
        JMenuItem diagramaItem = new JMenuItem("Diagrama de procesos");
        JMenuItem casosUsoItem = new JMenuItem("Casos de uso");
        JMenuItem presentacionItem = new JMenuItem("Presentación ejecutiva");
        JMenuItem manualUsuarioItem = new JMenuItem("Manual de usuario");
        JMenuItem manualProgramadorItem = new JMenuItem("Manual de programador");
        ayudaMenu.add(diagramaItem);
        ayudaMenu.add(casosUsoItem);
        ayudaMenu.add(presentacionItem);
        ayudaMenu.add(manualUsuarioItem);
        ayudaMenu.add(manualProgramadorItem);
        menuBar.add(ayudaMenu);

        setJMenuBar(menuBar);

        nuevoItem.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                display.setText("0");
                historial.add("Nuevo");
                guardarEnBitacora("Nuevo");
            }
        });

        historicoItem.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                mostrarHistorial();
            }
        });

        display.addKeyListener(this);
    }

    private double evaluarExpresion(String operacion) {
        throw new UnsupportedOperationException("Not supported yet."); // Generated from nbfs://nbhost/SystemFileSystem/Templates/Classes/Code/GeneratedMethodBody
    }

    private class BotonListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            String command = e.getActionCommand();
            char firstChar = command.charAt(0);

            if (Character.isDigit(firstChar) || command.equals(".")) {
                if (display.getText().equals("0")) {
                    display.setText(command);
                } else {
                    display.setText(display.getText() + command);
                }
                Toolkit.getDefaultToolkit().beep();
            } else if (command.equals("=")) {
                String operacion = display.getText();
                double resultado = evaluarExpresion(operacion);
                display.setText(String.valueOf(resultado));
                historial.add(operacion + " = " + resultado);
                guardarEnBitacora(operacion + " = " + resultado);
                Toolkit.getDefaultToolkit().beep();
            } else {
                display.setText(display.getText() + " " + command + " ");
                Toolkit.getDefaultToolkit().beep();
            }
        }

        private double evaluarExpresion(String expresion) {
            String[] elementos = expresion.split(" ");
            double num1 = Double.parseDouble(elementos[0]);
            double num2;
            String operador;
            double resultado = num1;

            for (int i = 1; i < elementos.length; i += 2) {
                operador = elementos[i];
                num2 = Double.parseDouble(elementos[i + 1]);

                switch (operador) {
                    case "+":
                        resultado += num2;
                        break;
                    case "-":
                        resultado -= num2;
                        break;
                    case "*":
                        resultado *= num2;
                        break;
                    case "/":
                        resultado /= num2;
                        break;
                    default:
                        throw new IllegalArgumentException("Operador no válido");
                }
            }

            return resultado;
        }
    }

    private void guardarEnBitacora(String operacion) {
        try {
            DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");
            LocalDateTime now = LocalDateTime.now();
            String fechaHora = dtf.format(now);
            String registro = fechaHora + ": " + operacion;

            FileWriter fileWriter = new FileWriter("bitacoraCalculadora.txt", true);
            fileWriter.write(registro + "\n");
            fileWriter.close();
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    private void mostrarHistorial() {
        StringBuilder historialTexto = new StringBuilder();
        for (String operacion : historial) {
            historialTexto.append(operacion).append("\n");
        }
        JTextArea historialArea = new JTextArea(historialTexto.toString());
        historialArea.setEditable(false);
        JScrollPane scrollPane = new JScrollPane(historialArea);
        scrollPane.setPreferredSize(new Dimension(400, 300));
        JOptionPane.showMessageDialog(this, scrollPane, "Histórico de operaciones", JOptionPane.PLAIN_MESSAGE);
    }

    @Override
    public void keyTyped(KeyEvent e) {
        char c = e.getKeyChar();
        if (Character.isDigit(c) || c == KeyEvent.VK_PERIOD || c == KeyEvent.VK_BACK_SPACE) {
            Toolkit.getDefaultToolkit().beep();
        } else if (c == KeyEvent.VK_ENTER) {
            String operacion = display.getText();
            double resultado = evaluarExpresion(operacion);
            display.setText(String.valueOf(resultado));
            historial.add(operacion + " = " + resultado);
            guardarEnBitacora(operacion + " = " + resultado);
            Toolkit.getDefaultToolkit().beep();
        }
    }

    @Override
    public void keyPressed(KeyEvent e) {
    }

    @Override
    public void keyReleased(KeyEvent e) {
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            CalculadoraFranco ventana = new CalculadoraFranco();
            ventana.setVisible(true);
        });
    }
}
