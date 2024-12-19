import javax.swing.*;
import javax.swing.plaf.nimbus.NimbusLookAndFeel;
import javax.swing.text.*;
import java.awt.*;
import java.awt.event.ActionEvent;

public class TBO {
    public static void main(String[] args) {
        // Mengatur Look and Feel Nimbus
        try {
            UIManager.setLookAndFeel(new NimbusLookAndFeel());
        } catch (Exception e) {
            e.printStackTrace();
        }

        // Frame Utama
        JFrame frame = new JFrame("Pengaturan AC Otomatis");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setExtendedState(JFrame.MAXIMIZED_BOTH);
        frame.setLayout(new GridBagLayout());
        frame.getContentPane().setBackground(new Color(240, 248, 255));

        // Layout Constraints
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(10, 10, 10, 10);
        gbc.fill = GridBagConstraints.HORIZONTAL;

        // Komponen Input Sensor
        JLabel lblMQ3 = new JLabel("Sensor MQ-3 (Alkohol):");
        JTextField txtMQ3 = createNumericTextField();

        JLabel lblMQ4 = new JLabel("Sensor MQ-4 (Gas Metana):");
        JTextField txtMQ4 = createNumericTextField();

        JLabel lblMQ6 = new JLabel("Sensor MQ-6 (Gas LPG):");
        JTextField txtMQ6 = createNumericTextField();

        JLabel lblMQ7 = new JLabel("Sensor MQ-7 (Karbon Monoksida):");
        JTextField txtMQ7 = createNumericTextField();

        JLabel lblMQ135 = new JLabel("Sensor MQ-135 (Polusi Udara):");
        JTextField txtMQ135 = createNumericTextField();

        JLabel lblSuhu = new JLabel("Masukkan Suhu (°C):");
        JTextField txtSuhu = createNumericTextField();

        JButton btnProses = new JButton("Proses");
        JButton btnReset = new JButton("Reset");

        // Output Status
        JButton btnStatusPolusi = new JButton("Status Polusi: ");
        JButton btnStatusSuhu = new JButton("Status Suhu: ");
        JButton btnKategoriPolusi = new JButton("Kategori Polusi: ");
        JButton btnStatusUdara = new JButton("Status Udara: ");

        // Menambahkan Komponen ke Frame
        gbc.gridx = 0; gbc.gridy = 0; frame.add(lblMQ3, gbc);
        gbc.gridx = 1; frame.add(txtMQ3, gbc);

        gbc.gridx = 0; gbc.gridy = 1; frame.add(lblMQ4, gbc);
        gbc.gridx = 1; frame.add(txtMQ4, gbc);

        gbc.gridx = 0; gbc.gridy = 2; frame.add(lblMQ6, gbc);
        gbc.gridx = 1; frame.add(txtMQ6, gbc);

        gbc.gridx = 0; gbc.gridy = 3; frame.add(lblMQ7, gbc);
        gbc.gridx = 1; frame.add(txtMQ7, gbc);

        gbc.gridx = 0; gbc.gridy = 4; frame.add(lblMQ135, gbc);
        gbc.gridx = 1; frame.add(txtMQ135, gbc);

        gbc.gridx = 0; gbc.gridy = 5; frame.add(lblSuhu, gbc);
        gbc.gridx = 1; frame.add(txtSuhu, gbc);

        gbc.gridx = 0; gbc.gridy = 6; frame.add(btnProses, gbc);
        gbc.gridx = 1; frame.add(btnReset, gbc);

        gbc.gridx = 0; gbc.gridy = 7; frame.add(btnStatusPolusi, gbc);
        gbc.gridx = 1; frame.add(btnStatusSuhu, gbc);
        gbc.gridx = 0; gbc.gridy = 8; frame.add(btnKategoriPolusi, gbc);
        gbc.gridx = 1; frame.add(btnStatusUdara, gbc);

        // Mengatur Warna Tombol
        btnProses.setBackground(new Color(0, 123, 255));
        btnProses.setForeground(Color.WHITE);
        btnReset.setBackground(new Color(220, 53, 69));
        btnReset.setForeground(Color.WHITE);

        btnStatusPolusi.setBackground(Color.GRAY);
        btnStatusSuhu.setBackground(Color.GRAY);
        btnKategoriPolusi.setBackground(Color.GRAY);
        btnStatusUdara.setBackground(Color.GRAY);

        // Logika Tombol Proses
        btnProses.addActionListener((ActionEvent e) -> {
            try {
                // Ambil Input Sensor
                int mq3 = Integer.parseInt(txtMQ3.getText());
                int mq4 = Integer.parseInt(txtMQ4.getText());
                int mq6 = Integer.parseInt(txtMQ6.getText());
                int mq7 = Integer.parseInt(txtMQ7.getText());
                int mq135 = Integer.parseInt(txtMQ135.getText());
                int suhu = Integer.parseInt(txtSuhu.getText());

                // Evaluasi Polusi per Sensor
                String kategoriPolusiMQ3 = evaluatePolusi(mq3, "MQ3");
                String kategoriPolusiMQ4 = evaluatePolusi(mq4, "MQ4");
                String kategoriPolusiMQ6 = evaluatePolusi(mq6, "MQ6");
                String kategoriPolusiMQ7 = evaluatePolusi(mq7, "MQ7");
                String kategoriPolusiMQ135 = evaluatePolusi(mq135, "MQ135");

                // Evaluasi Suhu
                String kategoriSuhu = evaluateSuhu(suhu);

                // Update Status Polusi
                btnStatusPolusi.setText(String.format("Polusi MQ3: %s, MQ4: %s, MQ6: %s, MQ7: %s, MQ135: %s",
                        kategoriPolusiMQ3, kategoriPolusiMQ4, kategoriPolusiMQ6, kategoriPolusiMQ7, kategoriPolusiMQ135));

                // Status Suhu
                btnStatusSuhu.setText("Status Suhu: " + kategoriSuhu);

                // Kategori Polusi
                String kategoriPolusi = getOverallPollutionStatus(kategoriPolusiMQ3, kategoriPolusiMQ4, kategoriPolusiMQ6, kategoriPolusiMQ7, kategoriPolusiMQ135);
                btnKategoriPolusi.setText("Kategori Polusi: " + kategoriPolusi);
                btnKategoriPolusi.setBackground(getColorForStatus(kategoriPolusi));

                // Status Udara
                String statusUdara = (kategoriSuhu.equals("Tinggi") || kategoriPolusi.equals("Tinggi")) ? "Bahaya" : "Aman";
                btnStatusUdara.setText("Status Udara: " + statusUdara);
                btnStatusUdara.setBackground(statusUdara.equals("Bahaya") ? Color.RED : Color.GREEN);

            } catch (NumberFormatException ex) {
                JOptionPane.showMessageDialog(frame, "Masukkan nilai berupa angka!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        });

        // Logika Tombol Reset
        btnReset.addActionListener(e -> {
            int confirm = JOptionPane.showConfirmDialog(frame, "Apakah Anda yakin ingin mereset semua input?", "Konfirmasi Reset", JOptionPane.YES_NO_OPTION);
            if (confirm == JOptionPane.YES_OPTION) {
                txtMQ3.setText("");
                txtMQ4.setText("");
                txtMQ6.setText("");
                txtMQ7.setText("");
                txtMQ135.setText("");
                txtSuhu.setText("");
                btnStatusPolusi.setText("Status Polusi: ");
                btnStatusSuhu.setText("Status Suhu: ");
                btnKategoriPolusi.setText("Kategori Polusi: ");
                btnStatusUdara.setText("Status Udara: ");
                btnStatusPolusi.setBackground(Color.GRAY); // Reset warna
                btnStatusSuhu.setBackground(Color.GRAY); // Reset warna
                btnKategoriPolusi.setBackground(Color.GRAY); // Reset warna
                btnStatusUdara.setBackground(Color.GRAY); // Reset warna
            }
        });

        frame.setVisible(true);
    }

    // Metode untuk membuat TextField hanya menerima angka
    private static JTextField createNumericTextField() {
        JTextField textField = new JTextField();
        ((AbstractDocument) textField.getDocument()).setDocumentFilter(new NumericDocumentFilter());
        return textField;
    }

    // Filter untuk validasi input angka
    static class NumericDocumentFilter extends DocumentFilter {
        @Override
        public void insertString(FilterBypass fb, int offset, String string, AttributeSet attr) throws BadLocationException {
            if (string != null && string.matches("\\d*")) {
                super.insertString(fb, offset, string, attr);
            }
        }

        @Override
        public void replace(FilterBypass fb, int offset, int length, String string, AttributeSet attr) throws BadLocationException {
            if (string != null && string.matches("\\d*")) {
                super.replace(fb, offset, length, string, attr);
            }
        }
    }

    // Evaluasi Polusi
    private static String evaluatePolusi(int value, String sensor) {
        if (value > 300) {
            return "Tinggi";
        } else if (value > 150) {
            return "Sedang";
        } else {
            return "Rendah";
        }
    }

    // Evaluasi Suhu
    private static String evaluateSuhu(int suhu) {
        if (suhu > 30) {
            return "Tinggi";
        } else if (suhu > 20) {
            return "Sedang";
        } else {
            return "Rendah";
        }
    }

    // Mengambil status Polusi keseluruhan
    private static String getOverallPollutionStatus(String... categories) {
        for (String category : categories) {
            if (category.equals("Tinggi")) {
                return "Tinggi";
            }
        }
        return "Rendah";
    }

    // Mengambil warna untuk status
    private static Color getColorForStatus(String status) {
        switch (status) {
            case "Tinggi":
                return Color.RED;
            case "Sedang":
                return Color.ORANGE;
            default:
                return Color.GREEN;
        }
    }
}
