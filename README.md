package todolist;
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.*;
import java.util.Scanner;

public class main {
    private JFrame frame;
    private DefaultListModel<String> listModel;
    private JList<String> todoList;
    private JTextField inputField;
    private JButton addButton, deleteButton, updateButton, saveButton;
    private int taskCounter = 1; // Görev numaralandırma için sayaç
    private final String FILE_NAME = "todolist.txt"; // Kayıt dosyası

    public main() {
       
        frame = new JFrame("To-Do List");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 400);
        frame.setLayout(new BorderLayout());

       
        listModel = new DefaultListModel<>();
        todoList = new JList<>(listModel);
        JScrollPane scrollPane = new JScrollPane(todoList);
        frame.add(scrollPane, BorderLayout.CENTER);

       
        JPanel panel = new JPanel();
        panel.setLayout(new FlowLayout());

        inputField = new JTextField(15);
        addButton = new JButton("Ekle");
        deleteButton = new JButton("Sil");
        updateButton = new JButton("Güncelle");
        saveButton = new JButton("Kaydet");

        panel.add(inputField);
        panel.add(addButton);
        panel.add(deleteButton);
        panel.add(updateButton);
        panel.add(saveButton);

        frame.add(panel, BorderLayout.SOUTH);

        // Önceki görevleri yükle
        loadTasks();

        
        addButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String task = inputField.getText().trim();
                if (!task.isEmpty()) {
                    listModel.addElement(taskCounter + "- " + task);
                    taskCounter++;
                    inputField.setText("");
                }
            }
        });

        deleteButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int selectedIndex = todoList.getSelectedIndex();
                if (selectedIndex != -1) {
                    listModel.remove(selectedIndex);
                    renumberTasks();
                } else {
                    JOptionPane.showMessageDialog(frame, "Lütfen silmek için bir görev seçin.");
                }
            }
        });

        updateButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int selectedIndex = todoList.getSelectedIndex();
                if (selectedIndex != -1) {
                    String updatedTask = inputField.getText().trim();
                    if (!updatedTask.isEmpty()) {
                        listModel.set(selectedIndex, (selectedIndex + 1) + "- " + updatedTask);
                        inputField.setText("");
                    }
                } else {
                    JOptionPane.showMessageDialog(frame, "Lütfen güncellemek için bir görev seçin.");
                }
            }
        });

        saveButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                saveTasks();
            }
        });
    }

    // Görevleri yeniden numaralandır
    private void renumberTasks() {
        DefaultListModel<String> newListModel = new DefaultListModel<>();
        for (int i = 0; i < listModel.size(); i++) {
            String task = listModel.get(i).substring(listModel.get(i).indexOf("-") + 2); // Eski numarayı kaldır
            newListModel.addElement((i + 1) + "- " + task); // Yeni numarayla ekle
        }
        listModel = newListModel;
        todoList.setModel(listModel);
        taskCounter = listModel.size() + 1; // Sayaç güncelle
    }

    private void saveTasks() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_NAME))) {
            for (int i = 0; i < listModel.size(); i++) {
                writer.write(listModel.get(i));
                writer.newLine();
            }
            JOptionPane.showMessageDialog(frame, "Görevler kaydedildi!");
        } catch (IOException e) {
            JOptionPane.showMessageDialog(frame, "Kaydetme sırasında hata oluştu!");
        }
    }

    // Önceki görevleri yükle
    private void loadTasks() {
        File file = new File(FILE_NAME);
        if (file.exists()) {
            try (Scanner scanner = new Scanner(file)) {
                while (scanner.hasNextLine()) {
                    listModel.addElement(scanner.nextLine());
                }
                taskCounter = listModel.size() + 1;
            } catch (IOException e) {
                JOptionPane.showMessageDialog(frame, "Görevler yüklenirken hata oluştu!");
            }
        }
    }

    public void show() {
        frame.setVisible(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                main app = new main();
                app.show();
            }
        });
    }
}
