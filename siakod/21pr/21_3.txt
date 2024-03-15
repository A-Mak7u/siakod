using System;
using System.Collections.Generic;
using System.IO;

// Узел бинарного дерева
public class TreeNode
{
    public int Value;           // Значение узла
    public TreeNode Left;       // Левый дочерний узел
    public TreeNode Right;      // Правый дочерний узел

    // Конструктор для узла дерева
    public TreeNode(int value)
    {
        Value = value;          // Устанавливаем значение узла
        Left = null;            // Инициализируем левый дочерний узел как null
        Right = null;           // Инициализируем правый дочерний узел как null
    }
}

// Класс для построения и работы с бинарным деревом поиска
public class BinarySearchTree
{
    private TreeNode root;      // Корень дерева

    // Конструктор для бинарного дерева поиска
    public BinarySearchTree()
    {
        root = null;            // Инициализируем корень дерева как null
    }

    // Метод для вставки нового узла в дерево
    public void Insert(int value)
    {
        root = InsertRecursive(root, value);    // Вызываем рекурсивный метод вставки
    }

    // Рекурсивный метод для вставки нового узла в дерево
    private TreeNode InsertRecursive(TreeNode current, int value)
    {
        if (current == null)
        {
            return new TreeNode(value);    // Если текущий узел null, создаем новый узел
        }

        if (value < current.Value)
        {
            current.Left = InsertRecursive(current.Left, value);  // Рекурсивно вставляем в левое поддерево
        }
        else
        {
            current.Right = InsertRecursive(current.Right, value); // Рекурсивно вставляем в правое поддерево
        }

        return current;
    }

    // Метод для проверки, является ли дерево сбалансированным
    public bool IsBalanced()
    {
        return IsBalancedRecursive(root);
    }

    // Рекурсивный метод для проверки, является ли дерево сбалансированным
    private bool IsBalancedRecursive(TreeNode node)
    {
        if (node == null)
        {
            return true;    // Для пустого узла возвращаем true
        }

        // Рекурсивно проверяем баланс поддеревьев
        int leftHeight = Height(node.Left);
        int rightHeight = Height(node.Right);

        return Math.Abs(leftHeight - rightHeight) <= 1 &&
               IsBalancedRecursive(node.Left) &&
               IsBalancedRecursive(node.Right);
    }

    // Метод для вычисления высоты дерева
    private int Height(TreeNode node)
    {
        if (node == null)
        {
            return 0;   // Для пустого узла возвращаем 0
        }

        // Рекурсивно вычисляем высоту поддеревьев
        int leftHeight = Height(node.Left);
        int rightHeight = Height(node.Right);

        // Возвращаем максимальную высоту плюс 1 (текущий уровень)
        return Math.Max(leftHeight, rightHeight) + 1;
    }

    // Метод для проверки, можно ли удалить не более n узлов, чтобы дерево осталось деревом бинарного поиска
    public bool CanDeleteNodes(int n, out List<TreeNode> deletableNodes)
    {
        // Проверяем, является ли дерево сбалансированным
        bool isBalanced = IsBalanced();

        if (!isBalanced) // Если дерево не сбалансировано, сразу возвращаем false
        {
            deletableNodes = null;
            return false;
        }

        // Если дерево сбалансировано, проверяем, можно ли удалить узлы
        deletableNodes = new List<TreeNode>();
        int count = 0;
        CanDeleteNodesRecursive(root, n, deletableNodes, ref count);

        // Возвращаем true, если удаление возможно
        return count <= n;
    }

    // Рекурсивный метод для проверки, можно ли удалить не более n узлов
    private void CanDeleteNodesRecursive(TreeNode node, int n, List<TreeNode> deletableNodes, ref int count)
    {
        if (node == null || count >= n)
        {
            return;
        }

        // Рекурсивно проверяем удаление узлов в левом поддереве
        CanDeleteNodesRecursive(node.Left, n, deletableNodes, ref count);

        // Если удаление еще возможно, добавляем текущий узел в список удаляемых
        if (count < n)
        {
            deletableNodes.Add(node);
            count++;
        }

        // Рекурсивно проверяем удаление узлов в правом поддереве
        CanDeleteNodesRecursive(node.Right, n, deletableNodes, ref count);
    }
}

class Program
{
    static void Main(string[] args)
    {
        // Считываем последовательность чисел из файла
        string[] lines = File.ReadAllLines("C:\\Users\\Maksimka\\source\\repos\\siakod\\siakod\\input.txt");
        int[] numbers = Array.ConvertAll(lines, int.Parse);

        // Создаем бинарное дерево поиска и вставляем все числа
        BinarySearchTree bst = new BinarySearchTree();
        foreach (int number in numbers)
        {
            bst.Insert(number);
        }

        // Проверяем, можно ли удалить не более 2 узлов
        bool canDeleteNodes = bst.CanDeleteNodes(2, out List<TreeNode> deletableNodes);

        // Записываем результаты в файл output.txt
        using (StreamWriter writer = new StreamWriter("C:\\Users\\Maksimka\\source\\repos\\siakod\\siakod\\output.txt"))
        {
            if (canDeleteNodes && deletableNodes.Count > 0)
            {
                writer.WriteLine($"Можно удалить следующие узлы для сбалансированности:");
                foreach (TreeNode node in deletableNodes)
                {
                    writer.WriteLine(node.Value);
                }
            }
            else
            {
                writer.WriteLine($"Нельзя удалить не более 2 узлов или нет узлов для удаления.");
            }
        }
    }
}
